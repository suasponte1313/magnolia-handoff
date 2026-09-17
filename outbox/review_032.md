# Review — loop 32 — Phase 3 §1 — Locked Framing Solver
**Verdict: REVISE**

### Verified from artifacts
- Performance directive implemented: search every 6th frame, verify full density — confirmed in solver_stdout.txt and JSON structure
- Fenton (CAM_A) all 4 shots PASS — JSON verify sections show pass=true, all hr_med and el_med within bands [0.03,0.18] and [0.26,0.44]
- Saba shot_1 NO DETECTION — JSON verify.shot_1=null confirmed
- Saba shot_2 FAIL — JSON shows hr_med_ok=false, hr_med_min=0.030 at band boundary, hr_raw_min=0.017 below band
- Saba shot_3 FAIL — JSON shows hr_med_max=0.511, el_med_max=0.621, both massively outside bands (misdetection outliers)
- Saba shot_4 PASS — JSON shows pass=true, hr_med=[0.042,0.178], el_med=[0.291,0.335] within bands
- Head size ratio 1.497x within 1.60 ceiling — JSON confirms
- Solver runtime ~74s — stdout timing sums confirm

### Unverified / suspicious
- Root cause of Saba detection failures not investigated — shot_1 had 0/146 detections, shots 2-3 had outlier frames. Are these lighting, skin tone, occlusion, or detection bug?

### Blocking
- Saba shots 1-3 fail verification. Before tuning crops or widening bands, must determine whether failures are detection errors (spurious outliers from bad frames) or actual framing violations (real skull-clip/eyeline breach)

### Carried (non-blocking)
- Fenton framing is solved — 4/4 shots pass at full density with crop [0,790,1204,1070]
- Head size ratio 1.497x acceptable but will shift if Saba crop changes

## Next prompt for AG  [SENT to AG]
```
Saba detection failures need diagnosis before tuning. Do this:

1. For Saba shot_3 (worst outliers: hr_med_max=0.511, el_med_max=0.621), dump the frame indices where hr > 0.30 or el > 0.50. These are the misdetection frames.

2. Extract 3-5 of those outlier frames as PNGs. Visually inspect: is there a face detected? Is the skin mask spurious (detecting background, hair, clothing)? Is the face occluded/turned?

3. For Saba shot_1 (0/146 detections), extract frames 0, 50, 100 as PNGs. Inspect: is Saba visible? Is skin detection failing due to lighting/tone/position?

4. Report: (a) the outlier frame indices, (b) your visual diagnosis of what's going wrong, (c) whether this is fixable via outlier rejection, detection parameter tuning, or requires switching to face-box detection instead of skin detection.

Include the extracted PNGs in the report dir. Do not tune crops or bands yet — we need to understand the detection failure mode first.
```