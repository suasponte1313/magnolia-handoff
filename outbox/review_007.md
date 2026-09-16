# Review — loop 7 — Phase 3 §1.1/§1.2 (ANALYZE_FACES / ANALYZE_SPEAKERS re-architecture)
**Verdict: BLOCK**

### Verified from artifacts
- Confidence literal removed: confidence_grep.txt shows tracks.py:240 appends round(det_conf,3) and :249 appends 0.0 for gaps — no hardcoded 1.0 assignment in the face-track confidence path.
- Face-track confidence is genuinely measured/noisy: spk_a confidence array in face_tracks_c1.json ranges ~0.451–0.826, no flat 1.000. confidence_dist.txt (min 0.465/max 0.712 spk_a, min 0.451/max 0.826 spk_b) matches the raw arrays. This claim is real.
- home_region is derived per clip, not hardcoded: c1=[2109,825,599,676], c2=[2104,857,598,662], c3=[2108,836,602,666] differ across clips — consistent with first-frame detection, not a literal ROI.
- mouth_motion_c1.txt is real per-frame optical motion (noisy, non-zero, both channels active, occasional 1.0000 peaks from per-frame normalization) — not a placeholder.
- speaker_timeline_*.json frame counts match the report exactly (c1: 578/440/1/181; c2: 185/662/5/48; c3: 276/318/0/6) and agreement rates match (0.4409, 0.7228, 0.4702). Report numbers are traceable to the raw files.
- All three clips are flagged:true, correctly honoring the <0.8 agreement rule from §1.2.

### Unverified / suspicious
- SUSPICIOUS PERFECTION IN speaker_timeline confidence: the *speaker-frame* confidence field is riddled with exact 1.000 values (c1 frames 7,23,36,39,41,80,...; c2 frames 199-214 nearly all 1.0; c3 frames 27-31,80-86,151-155,178,183-185,... long runs of 1.0). This is the very hardcoded-1.0 pattern the report claims to have eliminated — it was removed from tracks.py (face confidence) but appears untouched in speaker.py's per-frame confidence. No grep of speaker.py was provided to prove where these 1.0s come from.
- AGREEMENT RATE IS A FAILURE, NOT A PASS: §1.2 says 'if below 0.8, flag the clip.' All three clips are 0.44/0.72/0.47 — every clip fails the VAD/diarization agreement threshold. The report frames 'flagged:true across all three' and 'successfully passing the validation gate' as success. A 0.44 agreement rate means the two evidence sources disagree more than half the time; that is a red flag about the fusion, not a green light. No artifact explains WHY agreement is this low or that it's acceptable.
- c3 is effectively single-speaker-dominated and mislabeled at the top: speaker_timeline_c3.json frames 0-231 are almost entirely spk_b, then flips to spk_a — but face_tracks_c3 spk_a's home_region is at x~2108 (right side) while the boxes at frame 0 are x~134 (left side). The track_id→physical-person mapping is not proven consistent; no artifact ties spk_a/spk_b labels to a stable physical identity across clips.
- The 0.8 seed confidence: every timeline's frame 0 is exactly confidence 0.8 (c1, c2, c3 all start 'spk_a/spk_b, confidence:0.8'). That looks like a hardcoded initialization constant leaking into a reported measurement. Not proven otherwise.
- Weights 0.55 (mouth) / 0.45 (diarization) are asserted in prose but no grep/preset artifact was included showing they are preset-loaded rather than hardcoded literals in speaker.py.
- '100 passing unit tests' — no test stdout .txt was included. UNVERIFIED per no-claim-without-artifact.
- The channel-layout assumption is now claimed abandoned ('Silero VAD strictly as speech-vs-silence gate, per-channel attribution disproven') — but no ffprobe -show_streams / per-channel astats artifact was included to PROVE the mono/stereo question one way or the other. The standing rule requires that proof before anything downstream is baked in. It's still open.
- This report covers §1.1 AND §1.2 together and self-declares held 'prior to §1.3'. That is at the review boundary, acceptable in scope, but the substance fails.

### Blocking
- speaker.py per-frame confidence contains long runs of exact 1.000 — either prove (grep + assignment line) it is a genuine fused/normalized measurement, or it is the same hardcoded-perfection defect the report claims to have killed. Show the code that produces the SpeakerFrame.confidence value and a distribution of it.
- Agreement rate 0.44/0.72/0.47 all fail the 0.8 gate. Do not report this as 'passing.' Explain the low agreement with a raw artifact (per-frame VAD-vs-diarization disagreement breakdown) and state whether the fusion is trustworthy at this agreement level.
- No ffprobe -show_streams + per-channel astats artifact proving the source channel layout. The 'per-channel attribution disproven' claim must be backed by the raw stream/energy evidence, not asserted.
- No test stdout for the '100 passing tests' claim.
- frame-0 confidence 0.8 and weights 0.55/0.45 must be shown to be preset/derived, not literals.

### Carried (non-blocking)
- Step 0 gating items from the blueprint (0.1 REEL_CONFIGS removal, 0.2 headroom_pct/centre_x_pct literals, 0.3 measured subject_center_x from patched QC, 0.4 transcript revert+re-transcribe, 0.5 real verify_phase2 Check 1 diff, 0.6 LRA 8.2 / ROI first-frame / grep 233) — none appear addressed in this report and remain owed before §1.3.
- track_id → stable physical-person mapping across clips still unproven.

## Next prompt for AG
```
REVISE. Face-track confidence work is accepted (tracks.py:240/249 confirmed, distributions noisy and real, home_region derived per clip). But §1.2 is not done. Do NOT advance to §1.3. Fix and prove the following, RAW ONLY — no tables, no prose summaries substituting for files:

1. speaker.py confidence: `grep -n 'confidence' reelcore/src/reelcore/edit/speaker.py` and paste the exact line(s) that set SpeakerFrame.confidence. Then dump a distribution: `./venv/bin/python3 -c "import json,collections; d=json.load(open('speaker_timeline_c1.json'))['timeline']; import statistics as s; v=[f['confidence'] for f in d]; print('n',len(v),'min',min(v),'max',max(v),'mean',round(s.mean(v),3),'eq1.0',sum(1 for x in v if x==1.0))"` for c1, c2, c3. The runs of exact 1.000 must be explained by that assignment line or removed.

2. frame-0 = 0.8 seed and the 0.55/0.45 weights: paste the grep lines showing they are loaded from presets/edit/magnolia_interview.yaml, not literals in speaker.py.

3. Agreement rate: all three clips are 0.44/0.72/0.47 — BELOW the 0.8 flag threshold. Stop calling this 'passing.' Emit a per-frame VAD-vs-diarization disagreement artifact (agreement_breakdown_c1.txt: frame, vad_label, diar_label, agree bool) and state in one line whether fusion is trustworthy at 0.44. If the fusion is wrong, fix it; if 0.44 is expected for this footage, justify with the raw disagreement pattern.

4. Channel layout: `ffprobe -show_streams -select_streams a <demuxed source>` and per-channel `ffmpeg ... astats` (or the demux astats you already have) into channel_layout.txt. Prove whether there are two independent mono mics or one pre-mixed stereo, and show the second channel's energy is non-dead. This must land before any speaker logic is trusted.

5. Tests: `./venv/bin/python3 -m pytest reelcore/tests/ -q` redirected to test_stdout.txt. Include the file.

Separately: the Step 0 gating items (0.1-0.6) from PHASE3_BLUEPRINT are still owed and were not in this report. Confirm their status. STOP at this boundary; write report_008 and wait.
```