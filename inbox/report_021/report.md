# Report 021: §1.3 Closeout & §1.4 BUILD_CROPS (c1 Multi-Cam Proof)

## 1. Cut-on-Pause v2 (Formal Both-Sides Silence)
- Formal definition applied: `(cut_frame - p0 >= 250ms) and (p1 - cut_frame >= 250ms)` (where min_pause_ms = 250.0ms from presets/edit/magnolia_interview.yaml, 8 frames at 30fps).
- Cut 1 (f=137, t=4.567s): Inside pause [137, 155]. Left silence = 0.0ms (< 250ms), Right silence = 600.0ms (>= 250ms). Strict: False.
- Cut 2 (f=212, t=7.067s): Inside pause [205, 222]. Left silence = 233.3ms (7 frames, misses 250ms floor by 16.7ms / 0.5 frame), Right silence = 333.3ms (10 frames, >= 250ms). Strict: False.
- Cut 3 (f=662, t=22.067s): Inside pause [655, 662]. Left silence = 233.3ms (< 250ms), Right silence = 0.0ms (< 250ms). Strict: False.
- Strict both-sides pause fraction: 0.0000 (0 / 3).
- Single-side pause landing fraction: 1.0000 (3 / 3).
- 200ms tolerance fraction: 0.3333 (1 / 3).
- Raw artifact: `cut_on_pause_v2.txt`.

## 2. EDL Traceability & Logic
- Derivation logic in `reelcore/src/reelcore/edit/shots.py` lines 190–257.
- Reaction-shot trigger: When an active speaker's continuous run exceeds min_listen_frames + min_shot_frames (120f + 75f = 195f), cuts to the listener at the earliest inter-word pause p0 satisfying (c_start + min_listen_frames) <= p0.
- 75-frame min_shot_frames clamp: Enforced via `r_end = r_start + min_shot_frames` and `snapped_cut - cuts[-1] >= min_shot_frames` (75f = 2.5s from magnolia_interview.yaml), ensuring no rapid micro-cuts.
- Raw artifact: `edl_logic.txt`.

## 3. §1.4 BUILD_CROPS & Multi-Cam Render (c1 Only)
- Quantized crop path generated via `reelcore.edit.crops.build_crops` into per-shot stationary holds and eased moves.
- Hold fraction: 98.0% (total 1200 frames, 0px intra-shot camera jitter during holds).
- Look-room anchoring: Derived from preset `facing_direction` and `target_center_x_ratio` (0.35 for Fenton facing camera-right, 0.65 for Saba facing camera-left).
- Headroom targeting: Derived from `headroom_min_ratio` (0.08) and `headroom_max_ratio` (0.15).
- Punch-in framing: Smoothstep easing over 12 frames (`push_in_frames: 12`, scale 1.08) applied to Shot 3 (15s dialogue hold) and Shot 4 (17.9s dialogue hold).
- Re-rendered `output/PHASE_3_CONVERSATIONAL/c1_proof.mp4` with per-frame crops applied from `crops_c1.json`.
- QC verification executed:
  - Resolution: 1080x1920 (PASS)
  - Encoder: lavc62.28.101 libx264 (PASS)
  - Color metadata: tagged (PASS)
  - Loudness: -14.3 LUFS (PASS, target -14.0 +/- 1.0)
  - True Peak: -1.3 dBTP (PASS, target <= -1.0)
  - Loudness Range: 7.3 LU (PASS, target <= 11.0)
  - Room Resonance: 575 Hz +7.2 dB (PASS, target < +10 dB)
  - Head clipping left/right: 0% / 0% (PASS)
  - Subject centre X: 50.9% of width (PASS, target 30%-70%)
  - Left/right balance: 42% left / 58% right (PASS, neither half > 75%)
  - Headroom: 14.5% of height above head (PASS, target 2%-20%)
  - Framing stability: 179px std (WARN). Note on measurement: `qc.py` computes global std over all sampled frames of the file without shot/speaker segmentation. Because Fenton is framed at x=0.35 and Saba is framed at x=0.65 according to look-room rules, the bimodal switch across cuts produces a global std of 179px, while intra-shot hold stability has 0px camera drift.
  - Speech vs room tone: 7.2 dB (WARN, single-source audio reality, accepted in §1.2).
  - Overall status: PASSED (14 checks, 0 failed, 2 warnings).
- Raw artifacts: `crops_c1.json`, `c1_proof.qc.json`, `render_stdout_c1.txt`.
