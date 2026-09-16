# Review — loop 8 — Phase 3 §1.1/§1.2 (ANALYZE_FACES / ANALYZE_SPEAKERS)
**Verdict: BLOCK**

### Verified from artifacts
- channel_layout.txt: ffprobe shows ONE stereo aac stream (index 1, channel_layout stereo). astats confirms Ch1 RMS -30.39 dB vs Ch2 RMS -45.09 dB (~14.7 dB delta), Ch2 is a dead/ambient channel. The 'discrete per-mic VAD' assumption is correctly abandoned — single-track speech/silence gating is the only defensible path. This is a real, artifact-backed finding and I accept it.
- confidence_dist.txt: eq1.0=0 across all three speaker timelines and both face tracks; speaker means 0.728/0.776/0.766 within [0.500,0.950]. No flat-1.000 field. Spot-checked speaker_timeline_c1.json (0.794, 0.79, 0.513, 0.5...) — genuinely noisy, matches the distribution.
- confidence_grep.txt: speaker.py clips post_conf to [0.50,0.95] via sigmoid of margin (lines 397/406/413/419). No exact-1.0 clamp present. Consistent with the distributions.
- preset_weights_grep.txt: weight_mouth 0.55 / weight_diarization 0.45 / speech_threshold 0.25 live in presets/edit/magnolia_interview.yaml and are loaded via load_edit_preset(); speaker.py reads fusion_cfg, not hardcoded literals.
- All three timelines report flagged:true with agreement_rate below 0.8 (0.4409, 0.7228, 0.4702) — the flag logic fires as spec'd in §1.2.
- test_stdout.txt: 100 passed.

### Unverified / suspicious
- CORE DEFECT — diarization channel is degenerate/constant, so the reported agreement rate is meaningless. In agreement_breakdown_c1.txt the diar_label column is literally 'spk_a' for frames 0–654 (every single frame, through long silence gaps), then flips to 'spk_b' for the entire remainder. That is not word-level diarization from dallas1_transcript.json — it is two giant blocks. §1.2 requires the diarization evidence to come from the transcript's per-segment speaker field. A two-block step function is either a stub or a broken read. The 44% 'agreement' is just mouth-motion flipping against a constant.
- Because diarization is a step function, the 0.55/0.45 fusion is effectively mouth-motion-only for most of the clip, yet the report claims 'transcript diarization anchors true conversational turns.' The artifact contradicts the prose: diar anchors nothing here.
- mouth_motion_c1.txt shows the mouth-motion signal saturates to exactly 1.0000 constantly (frames 9,34,52,86,99,100,105,107,127,134-137...) and both speakers frequently spike together. This is per-frame normalized to a rolling max, so 'active speaker = argmax(mouth)' is dominated by noise — hence spk_a/spk_b thrash every 1–3 frames in the breakdown. The tiebreak source is being used as a primary and it is not reliable.
- speaker_timeline_c1 reports both_frames:1 and c3 both_frames:0 despite the c2 timeline containing multiple 'both' entries and heavy overlap:true runs — the both/overlap accounting is not obviously self-consistent; needs the counting code shown.
- face_tracks boxes contain obvious detector garbage that fill_detection_gaps was supposed to smooth: e.g. c1 frame 15 box height jumps 385→271 then back, repeated y=1184/1206/1222 spikes (~140px vertical jumps) every ~20 frames. §1.1 says 'hold last valid, never centre-fallback' — these transient jumps are not being held/rejected. facing flips left/right frame-to-frame with no smoothing.
- No §1.1/§1.2 metrics were produced that the blueprint requires downstream, and NO qc.json exists for any of this (Standing Rule 1). Report cites counts but there is no machine-readable QC sidecar for the speaker/face stage.
- Report is one enormous run-on paragraph asserting the fusion is 'fully trustworthy' — that conclusion is contradicted by its own artifacts and is exactly the 're-label a miss as a pass' pattern.

### Blocking
- Diarization evidence is a constant/two-block step function, not transcript-derived per-segment labels. Prove the diar_label column is read from dallas1_transcript.json's per-segment speaker field and show it changing at real segment boundaries — or the agreement metric and the whole fusion are invalid.
- Mouth-motion normalization saturates to 1.0000 and drives frame-to-frame speaker thrash; the median filter (window 9) is clearly not being applied before labeling, or is ineffective. Show the pre-smoothing vs post-smoothing speaker sequence and the switch count.
- Face tracks show unrejected detector jumps (height/y spikes) that fill_detection_gaps/hold-last-valid should have caught. Show the gap-fill applied.
- No qc.json for the ANALYZE_SPEAKERS / ANALYZE_FACES stage. Standing Rule 1 requires a machine-readable sidecar.

### Carried (non-blocking)
- Stereo-vs-dual-mono channel question is now PROVEN (single stereo, Ch2 dead). Carry forward into Phase 4 cross-talk: there is no independent inactive channel to attenuate — Phase 4 §2 mic de-bleed assumption must be revisited.
- Step 0 items (0.1–0.6) claimed 'verified and accepted' but NONE of their artifacts appear in report_008. Do not treat Step 0 as re-confirmed by this report.
- §1.2 requires reporting agreement rate AND flagging <0.8 — mechanics work, but the number is only meaningful once diarization is real.

## Next prompt for AG
```
BLOCK. Your confidence/preset/channel-layout work is accepted — the stereo source is proven single-track with a dead Ch2, and confidence is genuinely noisy from preset-loaded weights. But the fusion is not trustworthy and your report claims it is. Fix and re-report §1.2 only. Do NOT start §1.3.

1. DIARIZATION IS DEGENERATE. In agreement_breakdown_c1.txt the diar_label is 'spk_a' for EVERY frame 0–654 (including all the silence gaps) then 'spk_b' for the rest — a two-block step function, not transcript per-segment labels. Paste raw, no prose:
   - grep -n 'speaker' output showing where speaker.py reads dallas1_transcript.json segment speaker fields.
   - python one-liner dumping the transcript's segment (start,end,speaker) tuples for the c1 time range 945–985s: prove there are more than two turns and that diar_label changes at those boundaries. If the transcript really only has two speaker blocks in this window, say so and show it.

2. MOUTH MOTION SATURATES. mouth_motion_c1.txt hits exactly 1.0000 constantly and both speakers spike together, causing spk_a/spk_b to thrash every 1–3 frames. Show, raw:
   - the speaker sequence BEFORE the median filter and AFTER, for c1 frames 650–760, side by side, plus the total switch count before/after. If the window-9 median filter isn't being applied pre-label, fix it and re-run.

3. FACE GAP-FILL. face_tracks_c1 has box-height/y jumps (~140px) every ~20 frames (e.g. frame 15, 36-38, 79-81). §1.1 says hold-last-valid, never centre-fallback. Show the fill_detection_gaps code and paste 20 consecutive raw boxes proving transient jumps are rejected/held.

4. NO QC SIDECAR. Standing Rule 1: produce a speaker_qc.json for each clip with {agreement_rate, flagged, switch_count, both_frames, silence_frames, mean_confidence} and include the raw files.

Stop after re-reporting §1.2. One stage. Wait for prompt_009.
```