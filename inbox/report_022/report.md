# Report 022: Multi-Cam Framing QC, Cut Snapping, and Pipeline Traceability (c1)

## 1. Per-Shot Framing QC (§1.6)
- `reelcore.qc` updated to calculate framing stability per shot using `edl_c1.json` boundaries, while preserving the global multi-cam metric.
- Per-shot framing stability measurements:
  - Shot 1: frames [0 - 146] (4.87s) | CAM_A (spk_a) | 60.7px std (head turn at start) | crop motion: 0.0px
  - Shot 2: frames [146 - 221] (2.50s) | CAM_B (spk_b) | 23.3px std | PASS (< 25px std) | crop motion: 0.0px
  - Shot 3: frames [221 - 658] (14.57s) | CAM_A (spk_a) | 22.3px std | PASS (< 25px std) | 12f ease + hold
  - Shot 4: frames [658 - 1200] (18.07s) | CAM_B (spk_b) | 53.1px std (subject hand gestures into skin mask) | 12f ease + hold
- Camera crop tracks in `crops_c1.json` have 0.0px intra-shot camera drift (100% stationary holds with 12f smoothstep easing on punch-ins).
- Global multi-camera metric across cuts is 178px std (Fenton look-room ratio 0.35, Saba look-room ratio 0.65).
- Raw artifact: `framing_per_shot.txt`.

## 2. Cut Snapping & Cut-on-Pause v3
- Snapping logic in `reelcore/src/reelcore/edit/shots.py` updated to place cuts at the middle of detected pauses clamped by `min_shot_frames` (75f = 2.5s).
- New cut points:
  - Cut 1: frame 146 (t=4.87s) - pause midpoint [137, 155]. Left silence = 300.0ms, Right silence = 300.0ms. Strict both-sides >= 250ms: PASS.
  - Cut 2: frame 221 (t=7.37s) - pause [205, 222]. Left silence = 533.3ms, Right silence = 33.3ms. Strict both-sides >= 250ms: FAIL. Reason: Clamped by `min_shot_frames: 75` from frame 146. Landing at pause midpoint 213 would produce a 67-frame shot, violating the 75-frame editorial floor.
  - Cut 3: frame 658 (t=21.93s) - pause midpoint [655, 662]. Left silence = 100.0ms, Right silence = 133.3ms. Strict both-sides >= 250ms: FAIL. Reason: Physical pause between speakers is only 260.0ms total (966.820s to 967.080s). Mathematically, no cut within a 260ms gap can achieve 250ms silence on both sides (which requires >= 500ms total pause).
- Strict both-sides pause fraction: 1 / 3 = 0.3333. Single-side pause fraction: 3 / 3 = 1.0000.
- Raw artifact: `cut_on_pause_v3.txt`.

## 3. Pipeline Traceability & Preset Verification
- Pipeline upstream artifacts included in full:
  - `speaker_timeline_c1.json` (per-frame speaker assignments)
  - `edl_c1.json` (4 multi-cam shots with frames, timestamps, camera, subject, and transitions)
  - `face_tracks_c1.json` (per-frame face bounding boxes and confidences)
  - `crops_c1.json` (per-frame quantized crop windows in source px)
- `preset_grep.txt` verifies that 100% of framing, quantization, headroom, look-room, and EDL thresholds are loaded dynamically from `presets/framing/magnolia_interview.yaml` and `presets/edit/magnolia_interview.yaml` with zero hardcoded literals.

## 4. Channel Proof & Speaker Authority Chain
- Channel layout and energy re-verified from demux source:
  - `ffprobe_streams.txt` (AAC stereo stream #0:1, 48kHz, 182 kb/s)
  - `channel_energy.txt` (Ch1 RMS -37.2 dB vs Ch2 RMS -29.6 dB)
- `authority.txt` confirms speaker authority: diarization is the sole authority, mouth-motion weight is 0.0, per-mic VAD is disabled.

## 5. Re-rendered Master Proof & QC Gate
- `output/PHASE_3_CONVERSATIONAL/c1_proof.mp4` re-rendered with new cut placement and quantized crop path.
- Automated QC gate executed: 15 checks, 0 failed, 3 warnings (speech vs room tone 7.2 dB, framing stability per-shot max 61px std, framing stability global 178px reference metric).
- Raw artifacts: `c1_proof.qc.json`, `render_stdout_c1.txt`.
