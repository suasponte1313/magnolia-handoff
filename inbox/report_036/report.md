# Report 036: Crown Measurement Verification

The diagnostic PNGs confirm the crown line with 0.15 fallback was over-reading - placed 40-60px above actual head tops. Fixed the fallback to 0.03, which places the line much closer to actual crown positions. With corrected measurement, headroom reads 0.18-0.28 across all shots - genuinely excessive, not a measurement artifact. Crop re-solve to professional headroom (0.08) is infeasible: the required ~150px vertical shifts would push eyeline from 0.38 to 0.52, breaking the [0.28, 0.40] band. The source footage has subjects positioned too low in frame; both constraints cannot be satisfied simultaneously. Per Ruling 4, Dallas is degraded - recommend freezing c1 at current crops and moving engine tuning to Florida shoot.

## My Read on Crown Search

The fallback heuristic (face_top - k*face_height) was over-reading because in profile views, the face bounding box top often extends above the actual head crown. Reducing k from 0.15 to 0.03 places the crown line at or near the actual hair crown. The skin mask search fails because gray hair (Fenton) and dark hair (Saba) are not skin-colored. Even with perfect measurement, actual headroom is 22-28%, far exceeding the [0.05, 0.12] professional band.

## Per-Panel Per-Shot Metrics (Full Density)

### FENTON (crop: cx=0, cy=790, cw=1204, ch=1070)

| Shot | hr_med [min,max] | el_med [min,max] | Hard Gate | head_px | Unmeasured |
|------|------------------|------------------|-----------|---------|------------|
| shot_1 | [0.195, 0.272] | [0.335, 0.456] | PASS | 305.3 | 0 |
| shot_2 | [0.209, 0.243] | [0.364, 0.400] | PASS | 297.7 | 0 |
| shot_3 | [0.199, 0.281] | [0.345, 0.458] | PASS | 300.9 | 0 |
| shot_4 | [0.202, 0.276] | [0.350, 0.454] | PASS | 296.9 | 3 |

### SABA (per-shot crops)

| Shot | Crop | hr_med [min,max] | el_med [min,max] | Hard Gate | head_px | Unmeasured |
|------|------|------------------|------------------|-----------|---------|------------|
| shot_1 | cx=1528,cy=740,cw=1395,ch=1240 | [0.182, 0.229] | [0.261, 0.316] | PASS | 171.9 | 0 |
| shot_2 | cx=1474,cy=720,cw=1485,ch=1320 | [0.215, 0.232] | [0.284, 0.320] | PASS | 168.0 | 3 |
| shot_3 | cx=1528,cy=740,cw=1395,ch=1240 | [0.201, 0.248] | [0.274, 0.328] | PASS | 167.4 | 0 |
| shot_4 | cx=1582,cy=760,cw=1305,ch=1160 | [0.184, 0.257] | [0.262, 0.346] | PASS | 179.3 | 0 |

## Hard Gate Results

- **Skull clip count**: 0/1200 frames (all shots)
- **Back-of-head margin violations**: 0/1200 frames (all shots)
- **HARD GATES**: PASS 8/8

## Head Size Ratio

- Fenton mean: 300.2px
- Saba mean: 171.6px
- Ratio: 1.749 (ceiling 1.60 WAIVED per SOURCE_TIERS.md)

## Artifacts

- fenton_shot_1_f50_crown_diag.png - Crown line diagnostic
- fenton_shot_2_f180_crown_diag.png - Crown line diagnostic
- fenton_shot_3_f400_crown_diag.png - Crown line diagnostic
- saba_shot_1_f50_crown_diag.png - Crown line diagnostic
- saba_shot_2_f180_crown_diag.png - Crown line diagnostic
- saba_shot_3_f400_crown_diag.png - Crown line diagnostic
- fenton_shot1_f50_skin_mask_region.png - Skin mask visualization (0 pixels found)
- crown_diagnostic_summary.json - Sampled frame measurements
- solver_output_036.json - Full density measurements
