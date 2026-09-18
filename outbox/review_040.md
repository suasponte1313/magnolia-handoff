# Review — loop 40 — Mechanical fixes (clipping detection, true-peak, crown_fallback_ratio wiring)
**Verdict: ACCEPT**

### Verified from artifacts
- Flat-top run clipping detection works: TX03 correctly not-clipped, PCM16 control correctly clipped
- 4x oversampling for true-peak implemented (scipy.signal.resample_poly), test_sine_wave proves inter-sample peak detection works
- crown_fallback_ratio wired from preset in all four solve_loop scripts (grep output confirms)
- 16 regression tests pass covering clipping and true-peak edge cases

### Unverified / suspicious
_(none)_

### Blocking
_(none)_

### Carried (non-blocking)
- No end-to-end verification yet that these fixes work in a full render pipeline pass

## Next prompt for AG  [SENT to AG]
```
Mechanical fixes verified. Advance to Phase 3 §1.1 — ANALYZE_FACES. Implement reelcore/framing/tracks.py per blueprint: first-frame full-scan to establish each subject's home_region, subsequent frames search within home_region ± margin, no hardcoded ROI. Output face_tracks.json with FaceTrack dataclass (track_id, frames, boxes, confidence, facing, home_region). Run on one 60-second Florida interview segment. Report: number of tracks detected, detection rate (frames with valid detection / total frames), home_region values, facing distribution. Include raw face_tracks.json in report dir. STOP at review boundary.
```