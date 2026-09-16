# Review — loop 6 — Phase 3 §1.1/§1.2 (ANALYZE_FACES / ANALYZE_SPEAKERS)
**Verdict: BLOCK**  ·  ⛔ HUMAN GATE

### Verified from artifacts
- ffprobe_streams.json confirms ONE audio stream: index 1, aac, channels=2, channel_layout=stereo. There is exactly one pre-mixed stereo AAC track — NOT two independent mono mic tracks.
- channel_energy.txt astats confirms the two channels are wildly asymmetric: Ch1 RMS -30.43 dB / peak -9.00 dB vs Ch2 RMS -45.12 dB / peak -25.46 dB. Ch2 is ~15 dB quieter across the board. This is a stereo room/camera mic, not discrete per-speaker mics.
- speaker_timeline.json parsed: agreement_rate 0.9667, flagged=false, spk_a_frames=1693, spk_b_frames=0, both_frames=0, silence_frames=81. Confirms AG's report numbers.
- Per-frame confidence in speaker_timeline.json IS noisy/realistic (0.077 … 0.974 range), so the speaker timeline itself is not a flat placeholder.
- face_confidence.txt self-discloses that spk_b confidence is a hardcoded literal: tracks.py line 234 `track_data[tid]["confidence"].append(1.0)` — flat 1.000 across all 1,774 frames.

### Unverified / suspicious
- SUSPICIOUS PERFECTION / HARDCODED FIELD: spk_b confidence = 1.000 on every one of 1,774 frames, assigned by a literal `append(1.0)` at tracks.py:234. This is exactly the documented failure pattern. Confidence is not measured — it is a constant.
- spk_b_frames=0. The second subject is NEVER the active speaker for the entire clip, yet its face track reports perfect 1.000 confidence every frame. A track that is 'perfectly detected' but never speaks, over a segment where the quieter channel is 15 dB down, reads as a detector that always finds *a* blob in the ROI and rubber-stamps it — not a real per-frame detection.
- FOUNDATIONAL ASSUMPTION FALSIFIED: the report claims a 'fused active-speaker timeline fusing discrete-channel audio energy' and 'zero crosstalk'. The blueprint §1.2 explicitly requires independent channels for per-mic VAD and says 'verify at ingest'. ffprobe proves there are NO discrete channels — it's one stereo stream with an asymmetric room mix. Any speaker result derived from 'per-mic VAD on each demuxed channel' is built on an input that does not exist.
- 'zero crosstalk' is unverified and meaningless here: with one stereo mic you cannot separate speakers by channel. The 96.67% agreement and 95.43% spk_a assignment more likely reflect that Ch1 (the loud channel) dominates and spk_b simply never crosses threshold — not a working two-mic ASD.
- No face_tracks.json artifact was included. home_region ROIs [0,811,722,838] and [2102,844,598,661] are asserted in prose but the raw tracks JSON with per-frame boxes and the first-frame full-detection that supposedly derived them is not in the report dir. NO CLAIM WITHOUT ITS ARTIFACT.
- '100 passing unit tests' asserted with no raw pytest stdout artifact. UNVERIFIED.
- 'facing' per-frame field (left/right/camera) required by §1.1 — no artifact shows it was produced.

### Blocking
- Speaker fusion is built on a false input assumption. The standing rule is explicit: any speaker/crosstalk result depending on discrete Ch0/Ch1 is unverified until ffprobe proves the layout AND per-channel energy shows the second channel isn't dead. ffprobe proves the OPPOSITE — one stereo stream, Ch2 15 dB down. The VAD-per-channel design in §1.2 must be reworked (or the source re-examined for a separate multi-track file) before ANALYZE_SPEAKERS can be trusted.
- spk_b confidence is a hardcoded 1.0 literal (tracks.py:234). This violates 'measured, bounded, or preset — never a global constant' and is suspicious perfection. Face-track confidence must be a real measured quantity (blob area ratio, detector score, IoU-to-home_region, whatever), not append(1.0).
- Missing artifacts: face_tracks.json (with per-frame boxes, confidence, facing) and raw pytest stdout. Cannot accept §1.1/§1.2 without them.

### Carried (non-blocking)
- Step 0 gating items from the blueprint were never confirmed landed in a prior accepted loop (0.1 REEL_CONFIGS removal, 0.2 headroom/centre_x literals, 0.3 centre_x measured, 0.4 transcript re-transcribe, 0.5 real verify_phase2 Check 1, 0.6 LRA 8.2 / ROI first-frame / grep 233). If these are still open, §1 should not have started.
- grep for hardcoded ROI literals still owed per Step 0.6 — the home_region [0,811,...] / [2102,844,...] values must be shown to be derived per-clip, not baked in.
- Report agreement rate is above 0.8 so clip is unflagged — but re-evaluate once the single-stereo-source reality is accounted for; 96.67% may be an artifact of one dominant channel.

## Why this needs you
The core Phase 3 speaker-fusion design (§1.2, per-mic VAD on independent demuxed channels) is invalidated by the raw ffprobe: the source is a single pre-mixed stereo AAC stream with a 15 dB-asymmetric room mix, not two mono mics. This is a foundational input assumption that changes the architecture and needs the Commander's call — either locate the true multi-track source, or redesign ASD to run on a single mixed track (mouth-motion + diarization primary, no per-channel VAD). AG also shipped a hardcoded confidence=1.0 field and omitted required artifacts. Do not advance the build until Derek rules on the audio-source question.
