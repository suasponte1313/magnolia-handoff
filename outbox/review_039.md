# Review — loop 39 — Step 0 verification / Florida ingest / Audio sync
**Verdict: REVISE**  ·  ⛔ STOP

### Verified from artifacts
- face_framing.py lines 300-310 use variable name `crown_fallback_ratio`, not literal 0.03

### Unverified / suspicious
- crown_fallback_ratio source: grep shows the variable NAME is used, but not WHERE the value comes from. Need to see the assignment/load point to confirm it's from a preset, not `crown_fallback_ratio = 0.03` elsewhere in the file.
- Clipping claim contradicts artifact: AG claims 'TX03 mic is CLIPPED' but audio_analysis.txt shows 'Mic clipped samples (>=99% of peak): 1 (0.00%)' — that's 0.00% clipping. Either the analysis script is wrong or the clipping claim is wrong.
- Correlation values (-0.02 to -0.03) stated in prose but no raw scipy/numpy output artifact showing the actual correlation computation.
- Florida video ingest: no ffprobe output artifact proving the file exists at the claimed path with the claimed specs (3840x2160 HEVC, 30fps, 404.71s).

### Blocking
- Discrepancy between clipping claim and artifact must be resolved — either the mic IS clipped (prove it with correct analysis) or it ISN'T (retract the excuse for correlation failure).

### Carried (non-blocking)
- Dallas frozen per prior decision
- Step 0.1-0.6 closure status
- Audio sync for Florida remains unresolved

## Why the loop stopped (Commander must clear)
CIRCUIT BREAKER: 3 REVISE loops in a row with no progress — stopping for the Commander instead of re-tuning.

## Next prompt for AG  [RECOMMENDED — pending your approval]
```
Loop 39 REVISE. Three artifacts missing or contradictory:

1. crown_fallback_ratio: show WHERE the value comes from. Run: `grep -n 'crown_fallback_ratio.*=' reelcore/framing/face_framing.py` — I need to see the assignment line, not the usage line. If it's loaded from preset, show the load. If it's a constant at file top, that's still a hardcoded literal with a variable name.

2. Clipping claim vs. artifact: your audio_analysis.txt says 'Mic clipped samples: 1 (0.00%)' but your prose says 'TX03 mic is CLIPPED'. Which is true? If clipped, show peak at 0 dBFS and flat samples. If not clipped, retract the excuse and explain the real reason correlation failed.

3. Florida video: run `ffprobe -v error -show_format -show_streams input/florida/video/Video-Sep-13-2026-9-44-27-AM.mov 2>&1 | head -60` and include raw output.

Paste raw, no tables, no prose summaries. One artifact per claim.
```