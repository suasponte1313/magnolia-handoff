# Report 034 - Derived Bands and Unified Measurement Engine

## Reviewer Clarifications Resolved

**1. Detector Name**: The actual detector is OpenCV YuNet (FaceDetectorYN), not MediaPipe. The "mediapipe_face" label in solver_output_033.json was an incorrect hardcoded string. Grep output:
```
scripts/face_and_color_check.py:20:        import mediapipe as mp  # (unused legacy script)
reelcore/src/reelcore/face_framing.py:46:    """Face detector using OpenCV's YuNet (FaceDetectorYN)."""
```
The loop 032/033 solver imported from `reelcore.face_framing` which uses YuNet.

**2. Skin vs Face Comparison Artifact**: See `skin_vs_face_fenton_shot1.json`. On Fenton shot_1 (146 frames, crop [0,790,1204,1070]):
- Skin mask: hr_mean=0.061, range [0.033, 0.115]
- Face detector: hr_mean=0.185, range [0.145, 0.273]

The old band [0.06, 0.11] was calibrated against skin mask which measured blobs including hands/chest. Face detector measures face position only, yielding 3x higher headroom values.

**3. Band Definition Location**: `presets/framing/magnolia_interview.yaml:35`:
```yaml
locked_headroom_mean_band: [0.06, 0.11]
```
Now updated with derived bands.

## Commander Rulings Applied

**Ruling 1 (Single Engine)**: Implemented face detector only, no skin mask hybrid. `face_framing.py` updated, solver uses `measure_frame_face()` exclusively.

**Ruling 2 (Derived Bands)**: Calibrated from Fenton crop [0,790,1204,1070]:
- hr_mean_band: [0.16, 0.22] (target 0.19)
- hr_band: [0.12, 0.30]
- el_mean_band: [0.35, 0.42] (target 0.38)
- el_band: [0.30, 0.50]

Calibration derivation written into `presets/framing/magnolia_interview.yaml`.

**Ruling 3 (Eyeline Primary, Constrained Crown)**: 
- Eyeline measured from actual eye landmarks (YuNet provides right_eye, left_eye)
- Crown measured via `find_crown_constrained()`: skin mask search limited to column above face box
- Hard gates (skull clip, back-of-head margin) use crown position

**Ruling 4 (Head Size)**: Ratio = 1.749 (ceiling 1.60). Fenton mean head 300.2px, Saba mean head 171.6px.

## Results at Full Density

**Fenton (PASS 4/4):**
| Shot | el_mean | hr_mean | head_px | unmeasured | PASS |
|------|---------|---------|---------|------------|------|
| shot_1 | 0.375 | 0.185 | 305.3 | 0 | YES |
| shot_2 | 0.377 | 0.192 | 297.7 | 0 | YES |
| shot_3 | 0.380 | 0.190 | 300.9 | 0 | YES |
| shot_4 | 0.383 | 0.196 | 296.9 | 3 | YES |

**Saba (FAIL 0/4 - eyeline below band):**
| Shot | el_mean | hr_mean | head_px | unmeasured | PASS |
|------|---------|---------|---------|------------|------|
| shot_1 | 0.293 | 0.201 | 171.9 | 0 | NO |
| shot_2 | 0.296 | 0.214 | 168.0 | 3 | NO |
| shot_3 | 0.297 | 0.212 | 167.4 | 0 | NO |
| shot_4 | 0.309 | 0.211 | 179.3 | 0 | NO |

Saba's eyeline is at 0.29-0.31, below the derived band minimum of 0.35. Her headroom (0.20-0.21) is within band. The crops position her eyes closer to frame top (professional upper-third at 0.33) than Fenton's 0.38.

## Artifacts in This Directory

- `solver_output_034.json` - Full per-shot metrics with calibration data
- `skin_vs_face_fenton_shot1.json` - Comparison proving band miscalibration
- `report.md` - This report
