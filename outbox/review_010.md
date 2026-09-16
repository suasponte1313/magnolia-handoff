# Review — loop 10 — Phase 3 §1.2 ANALYZE_SPEAKERS + §1.1 gap-fill
**Verdict: BLOCK**

### Verified from artifacts
- Channel layout: ffprobe (channel_layout.txt) confirms single stereo AAC stream, channels=2, channel_layout=stereo — the pre-mixed-stereo reality is PROVEN, not two discrete mics. astats confirms Ch1 RMS -30.39 dB vs Ch2 RMS -45.09 dB (~14.7 dB lower). AG correctly treats VAD as speech-vs-silence only.
- No 1.000 saturation: confidence_dist.txt shows speaker timelines span 0.500–0.943, face tracks 0.000–0.826, all eq1.0=0. mouth_motion_c1.txt ma_max=0.6114 mb_max=0.9091, no saturation. Confirmed by spot-reading speaker_timeline_c1.json (values like 0.826, 0.679, 0.5).
- Fusion weights are preset-loaded, not hardcoded: preset_weights_grep.txt shows weight_mouth 0.55 / weight_diarization 0.45 / speech_threshold 0.25 in presets/edit/magnolia_interview.yaml, read via load_edit_preset in speaker.py:416-421.
- fill_detection_gaps (fill_detection_gaps_code.txt, quantize.py:379-421) holds last-valid, raises on all-gap, never centre-falls-back. face_continuity_c1.txt shows held frames (15, 36-38, 79-84) matching prior valid box exactly — real hold behavior.
- 100 unit tests pass (test_stdout.txt: '100 passed, 4 warnings in 7.14s').

### Unverified / suspicious
- c1 agreement rate 0.83 is measured against a DEGENERATE diarization track. In agreement_breakdown_c1.txt diar_label is spk_a for frames 0–654 (one continuous turn) and spk_b for ~662–1199. Agreeing 83% with a near-constant label is trivial and does NOT demonstrate the fusion attributes speakers correctly. AG's own note admits c1 '945–985s genuinely consists of only two speaker turns.' The gate (>=0.80) is therefore not a meaningful pass on c1.
- Mouth evidence is being OVERRIDDEN by diarization in exactly the frames that would test it. Frames 824–842: diar=spk_b, mouth_raw=spk_a, fused=spk_b, scored agree=True. Frames 893–962: repeated diar=spk_b vs mouth_raw=spk_a, fused follows diar. The 'primary' mouth signal (weight 0.55) is losing to 'secondary' diar (0.45) whenever they conflict — the opposite of the stated weighting — OR diar is dominating because mouth is noisy. Either way the report's claim that primary=mouth is unproven.
- switches_per_min / mean shot duration / min shot / wide fraction / cut-on-pause fraction — NONE reported. These are §1.2/§1.3 metrics the blueprint explicitly requires. speaker_qc.json reports switch_count but not per-minute rate or the shot metrics.
- 'both_frames' overlap on c1 (177) is asserted as 'genuine conversational overlap' but the raw timeline shows long 'both' runs at confidence flat 0.5 (frames 5-10, 20-24, 30-36, 51-56, 182-188, 251-263...). Confidence pinned at exactly 0.500 across every 'both' frame is suspicious — looks like a default assignment, not a measured overlap certainty. Need the assignment line that sets overlap confidence.
- Agreement rate denominator undefined in artifact. Report says c1 = 836/1007 during 'active speech' but silence_frames=184 and the breakdown counts False on many vad_speech=False rows — cannot reproduce 0.8302 from the raw file without the exact frame-selection rule.

### Blocking
- c1 agreement metric is not diagnostic because diar is degenerate (constant per half). Prove attribution another way: report agreement ONLY over frames where diar_label actually changes speaker within a short window, OR over a hand-checked ground-truth segment. A 0.83 against a two-value track is not acceptance-grade.
- Resolve the mouth-vs-diar override: with weight_mouth=0.55 > weight_diar=0.45, explain (from source + a worked frame) why fused follows diar on frames 824-842 and 893-962 where mouth_raw=spk_a. If diar is overriding primary evidence, the weighting is not doing what the report claims.
- The flat-0.500 confidence on every 'both' and every silence-boundary frame must be shown to be computed, not a hardcoded default. Paste the source lines that assign confidence for the 'both'/None branches.

### Carried (non-blocking)
- §1.3 BUILD_EDIT shot metrics (switches/min, mean/min shot, wide fraction, cut-on-pause) still owed.
- Step 0 items from prior loops: confirm centre_x measured from patched qc.py, transcript re-transcribe with vocabulary ('body'), verify_phase2 Check 1 real diff — none re-addressed in this report; do not lose them.
- Face home_region values (c1 [2109,825,599,676], c2 [2104,857,598,662], c3 [2108,836,602,666]) look derived per-clip and consistent — accept provisionally, but confirm they came from first-frame full detection, not a literal.

## Next prompt for AG
```
Do NOT advance to §1.3. Three blocking items on §1.2. Paste RAW only — no tables, no prose summaries.

1. c1 agreement is measured against a degenerate diarization track (diar_label is constant spk_a for frames 0-654, constant spk_b for ~662-1199 in agreement_breakdown_c1.txt). That makes 0.83 trivial. Recompute and report agreement over ONLY the frames within ±15 of a diar turn boundary (where diar actually changes), for c1/c2/c3. Paste the raw per-frame breakdown for those windows and the recomputed rate. If it drops below 0.80, that is the real number — report it.

2. Explain the mouth-vs-diar override. weight_mouth=0.55 > weight_diar=0.45, yet on frames 824-842 and 893-962 diar=spk_b, mouth_raw=spk_a, fused=spk_b. Paste the exact fusion code block from speaker.py (the lines computing the per-frame decision, ~440-500) and a hand-worked arithmetic for frame 835 showing how the weighted sum produces spk_b. If diar is overriding primary mouth evidence, say so.

3. Paste the source lines in speaker.py that assign `confidence` for the 'both' branch and the None/silence branch. Every 'both' frame is exactly 0.500 in speaker_timeline_c1.json — prove that is computed, not a hardcoded fallback.

4. State the exact frame-selection rule and denominator used to produce 0.8302 for c1, and show the arithmetic (numerator/denominator) from agreement_breakdown_c1.txt.

STOP after producing these artifacts. Do not touch §1.3, crops, or render.
```