# Review — loop 34 — Step 0 / Band Derivation
**Verdict: BLOCK**  ·  ⛔ STOP

### Verified from artifacts
- skin_vs_face_fenton_shot1.json shows real frame-by-frame comparison: skin_hr mean 0.061, face_hr mean 0.185 — confirms old band [0.06,0.11] was calibrated against skin mask, not face detector
- Fenton 4/4 shots pass: hr_mean in [0.185,0.196] within [0.16,0.22], el_mean in [0.375,0.383] within [0.35,0.42]
- Saba 0/4 shots fail on eyeline: hr_mean in [0.201,0.214] within band, but el_mean in [0.293,0.309] below band minimum 0.35
- Detector is OpenCV YuNet (grep artifact shows face_framing.py:46 uses FaceDetectorYN)
- Head size ratio 1.749 > ceiling 1.60 (Fenton 300.2px vs Saba 171.6px)

### Unverified / suspicious
- No grep output or preset file snippet showing the updated bands actually written to magnolia_interview.yaml

### Blocking
- Geometric conflict: bands derived from Fenton cannot be satisfied by Saba. Moving Saba's crop up to bring el from 0.29 to 0.35 (~74px on 1240ch crop) would drop hr from 0.20 to ~0.14, below band minimum 0.16. No valid crop exists for Saba that satisfies both bands.
- Per AGENTS.md framing priority stack, headroom+eyeline (priority 3) must be inside band, head size matching (priority 4) is sacrificed first. But head size is already exceeded (1.749 > 1.60), and priority 3 still fails. The spec is internally inconsistent for Saba.
- Commander ruling required: (a) derive per-subject bands, (b) widen eyeline band globally, (c) accept eyeline violation for Saba as priority 4, or (d) resize Saba crops larger at expense of coverage.

### Carried (non-blocking)
- Detector is OpenCV YuNet, not MediaPipe — naming corrected
- Skin mask retired; face detector is sole measurement engine

## Why the loop stopped (Commander must clear)
Fundamental spec conflict: Fenton-derived bands cannot be satisfied by Saba's geometry. Adjusting crop to fix eyeline breaks headroom and vice versa. This is a creative/editorial decision about how to frame a smaller subject relative to a larger one, not a tuning bug AG can fix.

## Next prompt for AG  [RECOMMENDED — pending your approval]
```
RECOMMENDATION (pending Commander ruling):

The derived bands are geometrically incompatible with Saba. Options:

1. **Per-subject bands**: Derive el_mean_band for Saba independently (measure her reference crop, build band around it). This accepts that a smaller subject in a two-shot naturally has eyes higher in frame.

2. **Widen global el_band**: Change el_mean_band from [0.35,0.42] to [0.28,0.42] to accommodate both subjects. Risk: band so wide it no longer constrains.

3. **Resize Saba crops larger**: Increase Saba's cw/ch to put her head_px closer to Fenton's ~300px. This would bring eyeline down but may clip edges or violate back-of-head margin.

4. **Reclassify eyeline as priority 4 for Saba only**: Accept el violation when head size ceiling is already breached. Document as deliberate creative choice.

AG: wait for Commander ruling on which path. Do not proceed.
```