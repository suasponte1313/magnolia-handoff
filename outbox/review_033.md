# Review — loop 33 — Phase 3 §1.1 — Face Detection Fix
**Verdict: REVISE**

### Verified from artifacts
- Saba shot_1 detection improved from 0/146 to 146/146 faces (solver_stdout.txt: 'skin=NULL (0 det) -> face_unmeas=0 (face_det=146)')
- Outlier frames 444-445 had skin components at pos=(316,571) and (316,555) — lower frame region, consistent with table/furniture misdetection (diagnosis_output.txt)
- All shots now have 100% detection rate with face detector (solver_output_033.json detection_stats)
- Face detector hr values cluster around 0.185-0.205 for all shots (solver_output_033.json hr_mean fields)

### Unverified / suspicious
- DISCREPANCY: report.md says 'OpenCV YuNet' but solver_output_033.json says 'mediapipe_face' — which detector was actually used?
- Claim 'hr=0.061 with skin mask' is asserted without raw artifact — no side-by-side measurement showing skin mask vs face detector on the same frames is provided
- Band values [0.06, 0.11] referenced but no artifact shows where these bands are currently defined (which preset file?)

### Blocking
- Clarify detector discrepancy: YuNet vs mediapipe_face — provide actual detector implementation path
- Provide raw artifact showing skin mask measurement on Fenton shot_1 frames for direct comparison (run skin mask on same crop, emit JSON with hr values)

### Carried (non-blocking)
- Head size ratio 1.746x exceeds ceiling 1.60x — must be addressed in crop selection
- All shots currently FAIL because hr bands [0.06, 0.11] don't match face detector output (~0.19)

## Next prompt for AG  [SENT to AG]
```
REVISE: Two discrepancies must be resolved before Commander ruling on bands.

1. DETECTOR NAME MISMATCH: report.md says 'OpenCV YuNet', solver_output_033.json says 'mediapipe_face'. Run: `grep -rn 'YuNet\|mediapipe\|FaceDetection' scripts/ reelcore/` and paste raw output. Which detector is actually implemented?

2. MISSING SKIN MASK COMPARISON: You claim hr=0.061 with skin mask vs hr=0.185 with face detector on Fenton shot_1. Provide raw artifact proving this. Run the skin mask measurement on Fenton shot_1 frames (crop [0,790,1204,1070]), emit JSON with per-frame hr values, include in report dir.

3. BAND DEFINITION: Run `grep -rn 'hr_.*band\|0\.06.*0\.11' presets/` and show where the [0.06, 0.11] band is defined.

Once these three are in raw artifacts, we can present the Commander with a clean ruling request: exact detector used, exact band definition, and measured comparison showing why bands must change.
```