# Review — loop 20 — Phase 3 §1.2/§1.3 (c1 corrections)
**Verdict: BLOCK**  ·  ⛔ STOP

### Verified from artifacts
- flag_fix.txt + speaker_timeline_c1.json agree: agreement_rate=0.4967, flagged=true. Flag logic is now real and computed from mouth-vs-diar disagreement.
- weights.txt confirms weight_mouth:0.0 / weight_diarization:1.0 in preset — mouth motion neutralized from cut timing, consistent with the 49.7% (chance-level) agreement.
- confidence is no longer a flat 0.700 field: speaker_timeline_c1.json shows a genuine spread (0.15, 0.23, 0.5, 0.702, 0.811, 0.848, 0.937, 0.98...) across frames. Suspicious-perfection on confidence is disproven for the non-null frames.
- ffprobe_streams.txt + channel_energy.txt confirm the source is ONE stereo AAC stream (channels=2, layout=stereo), and per-channel astats show Ch1 RMS -37.2 dB / Ch2 RMS -29.6 dB — both channels alive, not a dead second channel. This is a single stereo stream, NOT two discrete mono mics.
- verify_stdout.txt Check 1 is real: renders reel_1 to fresh path, runs QC, diffs 6 fields within tolerance (all PASS); Check 2 kills mid-caption and shows transcription call count 1 on restart; Check 3 shows warm-cache reuse. Step 0 verification stands.
- roi_grep.txt, step0_grep.txt empty; home_region_code.txt uses connectedComponentsWithStats and derives home_region from first-frame blob — no hardcoded ROI literal.
- fresh vs reference reel_1 qc.json are byte-identical on measured fields — consistent, deterministic render.

### Unverified / suspicious
- CUT-ON-PAUSE CLAIM IS FALSE ON ITS OWN ARTIFACT. shots_metrics_c1.txt claims cut_on_pause_fraction=1.0 (3/3). But cut_boundaries_c1.txt shows the CUT AT FRAME 137 lands on the LAST frame of spk_a speech (frames 132-136 = spk_a, 137 = None). The cut at 662 lands where spk_b STARTS (661=None, 662=spk_b) — that is cutting ONTO speech onset, not inside a pause. Only the 212 boundary is unambiguously inside silence. The blueprint says 'cut inside pauses, not mid-word'; a cut placed exactly on the speech-onset frame is not a pause cut. 3/3=1.000 is suspicious perfection and the raw frames contradict it.
- THE ENTIRE EDL LOOKS HAND-BUILT, NOT ALGORITHM-DERIVED. speaker_timeline_c1.json shows 37 switches and a jittery spk_a/spk_b/None sequence, yet the EDL is exactly 4 clean shots with Shot 2 = EXACTLY 75 frames (== min_shot_frames literal) and reason='reaction'. A reaction at frames 137-212 is not supported by the timeline: frames 137-154 are None (silence), spk_a resumes at 155. There is no spk_b speech in 137-212 to justify a CAM_B reaction shot. The shot list does not follow from the timeline the blueprint algorithm describes.
- Shot 2 duration = exactly min_shot_frames (75). Shot boundaries at 137/212/662 do not correspond to sustained speaker holds in the timeline (spk_a is active well past 662-ish region only intermittently). This smells like a per-clip hand-tuned EDL ('sweep to green then freeze'), not output of the BUILD_EDIT algorithm over speaker_timeline_c1.json.
- shot_list.json / crop_tracks.json from the actual BUILD_EDIT and BUILD_CROPS JobRunner stages were NOT provided. edl_c1.json is a summary EDL, not proof that shots.py produced it deterministically from the timeline. No test_shots.py / test_speaker.py / test_crops.py output included (§1.7).
- confidence for null/silence frames is hardcoded 0.50 (see frames 21-22, and the else branch in confidence_fix.txt appends 0.50). That is a constant standing in for a measurement on silence frames — acceptable as 'no speech token' but it must be labeled as a sentinel, not a confidence.
- _extract_word_probabilities_per_frame defaults word_probs to a hardcoded 0.85 fill and clips to [0.15,0.98]; the 0.98 ceiling explains the many flat 0.98 runs. This is a bounded transform of Whisper probs, acceptable, but the long runs of identical 0.98/0.811/0.848 are the transcript word-prob held across a word's frame span — fine, but confirm these are real per-word probs not a fill default.
- c1_proof.qc.json framing stability = 58px std (WARN) and subject_center_x 44.7% — the multi-cam crop is NOT quantized. Deferred to §1.4, acceptable as a WARN, carried.

### Blocking
- cut_on_pause metric reports 3/3=1.000 but raw cut_boundaries_c1.txt shows at least the frame-137 and frame-662 cuts land on speech-boundary frames, not inside pauses. Fix the metric definition (a pause cut must have ≥ min_pause_ms of None/silence on BOTH sides of the cut) and re-report the HONEST fraction from raw frames.
- The 4-shot EDL is not traceable to speaker_timeline_c1.json. Shot 2 (frames 137-212, CAM_B/spk_b, reason=reaction) is contradicted by the timeline (137-154 = None silence, 155+ = spk_a; no spk_b in that span). Prove BUILD_EDIT generated this EDL mechanically by running shots.py on the committed speaker_timeline_c1.json and emitting shot_list.json — do not hand-author the EDL.
- Shot 2 == exactly 75 frames (min_shot_frames literal) is a red flag for per-clip tuning. Show the algorithm derivation, not a baked shot.

### Carried (non-blocking)
- c1 framing stability 58px std + center_x 44.7%: multi-cam crop quantizer §1.4 must fix; do not accept close-up framing until quantized.
- c1 speech-vs-room-tone 7.2 dB WARN (vs reel_1's 16.6 dB) — this 40s slice has audible room tone; Phase 4 de-noise territory but note the discrepancy with the clean reel_1 chain.
- reel_1 room resonance 233 Hz +14.2 dB WARN and 90/10 left balance WARN still open from Step 0.
- §1.7 property tests (test_shots/test_speaker/test_crops) and the JobRunner registration of all four stages still owed for §1.8 sign-off.
- Source is a single stereo stream, not discrete mics — any future per-mic VAD speaker logic (blueprint §1.2 primary source) is INVALID for this footage; diarization-only is the correct fallback and is now in place. Keep this locked.

## Why the loop stopped (Commander must clear)

