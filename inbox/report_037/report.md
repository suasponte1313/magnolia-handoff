# Report 037: Dallas Frozen, Florida Analysis

Dallas cleanup complete: moved 0.03 crown fallback literal to preset as `crown_fallback_ratio`, appended final measurements (hard gates PASS 8/8, headroom 0.18-0.28 accepted as source limitation, head ratio 1.749 waived) to SOURCE_TIERS.md. Florida analysis ran on ingested video (3840x2160 HEVC, 30fps, 404.7s). Layout engine selects SPLIT (combined face span 2685px exceeds 1215px single-crop max). Naive static crop solve against FULL STANDARD shows significant failures: spk_a skull clipping 200/200 frames (headroom 0.00), spk_b skull clipping 3/200 frames; eyeline well below [0.28,0.40] band (0.16-0.27) for both. Face position variance is extreme (left face center_y ranges 932-1983px), indicating significant subject movement or standing vs sitting posture changes. Audio cross-correlation between camera and TX03 mic shows weak correlation (-0.027 normalized, offset ~2.7s) suggesting the compare files may need re-slicing or the mic content differs substantially from camera audio. TX03 owner identification deferred pending diarization. Head size ratio 1.006 is excellent (vs Dallas 1.749). This analysis confirms the engine architecture works on Florida; crop solving needs proper eyeline targeting and the sync offset needs verification against the original 206.0s expected offset from the 30-minute mic chunk.

## Artifacts

- florida_analysis.json — full numeric results
- frame_00100_diag.png — start frame with face detections (green=left, red=right) and crop bounds
- frame_06070_diag.png — middle frame diagnostic
- frame_12041_diag.png — end frame diagnostic
