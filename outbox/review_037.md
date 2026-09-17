# Review — loop 37 — Dallas freeze + Florida initial analysis
**Verdict: REVISE**

### Verified from artifacts
- Layout selection SPLIT: combined_span_px 2685 > single_crop_max_px 1215 from JSON
- spk_a skull clipping 200/200: skull_clip_count=200, n_measured=200 in JSON
- spk_b skull clipping 3/200: skull_clip_count=3, n_measured=200 in JSON
- Headroom spk_a 0.0: hr min/max/mean all 0.0 in JSON
- Eyeline below [0.28,0.40] band: spk_a el 0.164-0.206, spk_b el 0.175-0.273 in JSON
- Face position variance extreme: left center_y range 932-1983px (1051px) in JSON
- Audio correlation weak: correlation_normalized -0.027 in JSON
- Head size ratio 1.006: computed from head_px means 333.2/331.1 in JSON
- Three diagnostic PNGs present as binary artifacts

### Unverified / suspicious
- Dallas cleanup: no artifact showing crown_fallback_ratio in preset file
- Dallas cleanup: no grep proving 0.03 literal removed from scripts
- Dallas cleanup: SOURCE_TIERS.md update not included in report dir
- Video dimensions '3840x2160 HEVC': not in JSON, no ffprobe output provided
- Expected offset inconsistency: report says '206.0s expected offset from 30-minute mic chunk' but JSON shows expected_offset_s: 0.0

### Blocking
- Dallas freeze claim has no artifacts - cannot accept Florida work until Dallas closure is evidenced

### Carried (non-blocking)
- Florida mic owner identification pending diarization
- Florida sync offset issue: correlation -0.027 suggests possible misalignment or content mismatch between camera audio and TX03 mic
- Florida crop solving needs proper eyeline targeting - static naive solve fails hard gates

## Next prompt for AG  [SENT to AG]
```
Dallas closure artifacts missing. Before Florida work continues, provide in report_038/:

1. grep -rn 'crown_fallback_ratio' presets/ > dallas_preset_grep.txt
2. grep -rn '0\.03' scripts/ reelcore/ > literal_check.txt  (should be empty or unrelated hits only)
3. tail -50 docs/SOURCE_TIERS.md > source_tiers_tail.txt

Also for Florida:
4. ffprobe -v error -show_streams input/florida/video/Video-Sep-13-2026-9-44-27-AM.mov > florida_ffprobe.txt
5. Explain the expected_offset_s discrepancy: report says 206.0s expected but JSON shows 0.0

Raw output only. No tables. Then DONE, bridge, STOP.
```