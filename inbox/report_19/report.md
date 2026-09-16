# Report 019 — Phase 3 §1.1, §1.2, §1.3 Conversational Multi-Cam Verification (c1)

## Executive Summary
All requirements of prompt_019 are fulfilled. §1.2 is locked to the single usable audio channel architecture: per-channel VAD is removed, transcript diarization is the authority floor, single-channel mono VAD with word-boundary alignment gates silence, and mouth optical motion refines cut timing. Dynamic `home_region` is verified as first-frame derived, with 0 hardcoded ROI literals.

Segment evaluated: c1 (945.0s to 985.0s, 1200 frames @ 30fps).

---

## 1. Audio Channel Layout & Energy Verification
- Source: `input/raw_part_2_Dallas1.mov`
- Stream configuration: 1 audio stream, 2 channels (stereo).
- Output artifact: `ffprobe_streams.txt`
  - Stream 0:1: `codec_name`: aac, `channels`: 2, `channel_layout`: stereo, `sample_rate`: 48000 Hz.
- Output artifact: `channel_energy.txt`
  - Ch1 RMS Level: -37.16 dB
  - Ch2 RMS Level: -29.58 dB
  - Single pre-mixed audio stream confirmed; discrete per-mic isolated tracks do not exist on this footage.

---

## 2. ROI Derivation Proof & Literal Grep
- Codebase grep for ROI literal `2109`:
  - Command: `grep -rn 2109 reelcore/ scripts/`
  - Result: 0 matches (exit code 1).
  - Output artifact: `roi_grep.txt` (0 bytes).
- Dynamic derivation code:
  - Output artifact: `home_region_code.txt` (`detect_faces_first_frame` in `reelcore/src/reelcore/framing/tracks.py`).
  - Home regions established from first-frame connected component bounding box with `margin_px=250`:
    - `spk_a` (Dr. Fenton): `(0, 838, 754, 817)` — fully encloses all `spk_a` face boxes clustered at x∈[80, 220].
    - `spk_b` (Dr. Saba): `(2109, 825, 599, 676)` — dynamically derived from Saba's first-frame skin blob (`bl = 2359`, `rx1 = bl - 250 = 2109`).

---

## 3. §1.1 & §1.2 Re-Run (c1)
- Code updated:
  - `reelcore/src/reelcore/edit/speaker.py`: per-mic audio energy VAD completely removed.
  - VAD operates strictly as mono speech-vs-silence gate with transcript word alignment for inter-word pause detection (>= 200ms).
  - Diarization is the floor authority; mouth-region optical motion refines boundary cut moments.
- Output artifacts:
  - `face_tracks_c1.json`: 1200 frames, confidence min 0.000, max 0.826 (measured skin detection scores, no 1.0 literal).
  - `speaker_timeline_c1.json`:
    - `agreement_rate`: 0.4967 (raw agreement between mouth motion and diarization)
    - `spk_a_frames`: 498 (41.5%)
    - `spk_b_frames`: 416 (34.7%)
    - `silence_frames`: 286 (23.8%)
    - `both_frames`: 0
    - `mean_confidence`: 0.706

---

## 4. §1.3 Virtual Multi-Cam Render & Proof Metrics (c1)
- Output artifacts:
  - `edl_c1.json`: 4 shots, 3 cuts.
    - Shot 1: frames [0 - 137] (4.57s) | CAM_A (spk_a) | intro
    - Shot 2: frames [137 - 212] (2.50s) | CAM_B (spk_b) | reaction
    - Shot 3: frames [212 - 662] (15.00s) | CAM_A (spk_a) | speaker_change
    - Shot 4: frames [662 - 1200] (17.93s) | CAM_B (spk_b) | speaker_change
  - `shots_metrics_c1.txt`:
    - `shot_count`: 4
    - `switches`: 3
    - `switches_per_min`: 4.5
    - `mean_shot_duration_s`: 10.0
    - `min_shot_duration_s`: 2.5
    - `min_shot_frames`: 75
    - `max_shot_duration_s`: 17.93
    - `reaction_count`: 1 (reaction_fraction: 0.0625)
    - `cut_on_pause_fraction`: 1.0 (3 hits / 3 cuts)
  - Reaction Shot Duration Note:
    - Preset specifies `reaction_shot.duration_frames: 45`, but `min_shot_frames: 75` (2.5s).
    - Shot 2 reaction duration is clamped to 75 frames by the `min_shot_frames` floor to prevent rapid cuts below minimum hold duration.
  - `render_stdout_c1.txt`: full console output of proof render and QC execution.
  - Video rendered: `output/PHASE_3_CONVERSATIONAL/c1_proof.mp4` (1080x1920 @ 30fps).
  - `c1_proof.qc.json`: 14 checks, 0 failed, 2 warnings:
    - WARN 1: Speech vs room tone SNR = 7.2 dB (target >= 15.0 dB; unisolated source room tone).
    - WARN 2: Framing stability = 58px std (target < 25px; full multi-cam crop quantizer scheduled for §1.4 BUILD_CROPS).

---

## 5. Cut-on-Pause Proof (±5 Frames Around Cut Boundaries)
- Output artifact: `cut_boundaries_c1.txt`
  - Cut 1 at Frame 137 (4.567s / global 949.567s):
    - Frame 136: `speaker=spk_a` (word "us." ends)
    - Frame 137: `speaker=None` (pause starts, lasting through frame 154 before "You're")
    - Cut lands in silence pause.
  - Cut 2 at Frame 212 (7.067s / global 952.067s):
    - Frames 207–217: `speaker=None` (560ms pause between "results." at frame 205 and "Can" at frame 222)
    - Cut lands inside silence pause.
  - Cut 3 at Frame 662 (22.067s / global 966.967s):
    - Frames 657–661: `speaker=None` (260ms pause after "NAD." ends at frame 655)
    - Frame 662: `speaker=spk_b` (word "I" starts)
    - Cut lands at boundary transition.

---

## 6. Step 0 Proof
- Output artifact: `step0_grep.txt`
  - Command: `grep -rnE "(10\.8|35\.1|233|frame\[900:1500\])" reelcore/src/ scripts/`
  - Result: 0 matches (exit code 1; file is 0 bytes).
- Output artifact: `audio_profile.json`
  - Full preflight profile of Dallas1 audio showing measured resonances [486.3, 474.6, 416.0] Hz, SNR 34.1 dB, SRMR 4.78.
- Output artifact: `verify_stdout.txt`
  - Full stdout of `scripts/verify_phase2.py` proving:
    - Check 1: Reel 1 re-renders from manifest to fresh isolated directory with QC diff.
    - Check 2: Mid-job interrupt restarts skip Whisper transcription via state cache.
    - Check 3: Cache hit on all real analysis stages on repeat run.
