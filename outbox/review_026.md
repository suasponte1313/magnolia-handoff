# Review — loop 26 — Phase 3 §1.3/§1.4 — SPLIT layout proof (c1)
**Verdict: BLOCK**  ·  ⛔ STOP

### Verified from artifacts
- c1_proof.qc.json raw: passed=false, FAILED (26 checks, 6 failed, 1 warning). AG did not dress this up as a pass — honest failure disclosure confirmed against the raw JSON.
- Layout decision SPLIT is traceable: layout_decision_c1.txt shows combined margin span 2630.2px > 1215.0px ceiling with 2 faces → SPLIT. The 1215px ceiling = 2160*0.5625 comes from framing_preset.txt layout_engine.single_crop_max_width_ratio (preset-sourced, not a bare literal).
- Per-panel framing numbers in report.md match c1_proof.qc.json exactly: Top CAM_A headroom 0.0853/min 0.0544, back_margin_min 0.0198, lead_room 0.6361, center_x 0.2396; Bottom CAM_B headroom 0.1207, back_margin 0.2722, lead_room 0.6038. No fabricated numbers.
- framing_per_frame_dump.json is real per-frame data, noisy and non-flat (Fenton headroom drifts 0.059–0.135, Saba 0.20–0.27) — not suspicious perfection.
- Top CAM_A back-of-head FAIL (min 2.0% vs 4%) and lead-room FAIL (63.6% vs [35,46]) are genuine, confirmed in dump: Fenton box hits x=73 near sensor edge x=0.
- Bottom CAM_B headroom FAIL (mean 12.1% vs 12.0% ceiling) confirmed in JSON.

### Unverified / suspicious
- SUSPICIOUS PERFECTION: crops_c1.json hold_fraction=1.0 and EVERY one of 1200 frames has identical top_panel [0,765,1238,1100] and bottom_panel [1720,752,1080,960]. The crop is a STATIC frozen window — no quantize_path, no tracking. Yet face_tracks show subjects moving (Fenton x 73→218, Saba y drifting). This is a fixed-box two-shot, not the tracked panel §1.4 requires. The framing 'stability 15.9px std' PASS is trivially true because the crop never moves; the std is entirely subject motion inside a static box.
- SPEAKER TIMELINE UNVERIFIED: report cites active-speaker border 'synchronized to speaker_timeline_c1.json' and edl_c1.json labels shots 'speaker_change'/'reaction', but NO speaker_timeline_c1.json artifact was included and NO VAD/diarization evidence. Blueprint §1.2 requires per-mic VAD on independent channels — the qc.json shows audio_channels:1 (mono). The two-independent-mic-channels assumption is NOT proven; ASD is asserted, not measured.
- CHANNEL LAYOUT NOT PROVEN: no ffprobe -show_streams, no per-channel energy. Speaker assignment feeding the EDL cannot be trusted without it. Standing rule violated.
- EDL metrics claimed by blueprint §1.3 (switches/min, mean shot, cut-on-pause fraction, min_shot) are referenced (shots_metrics_c1.txt) but that file was NOT included in the report dir. Shot 2 is 67 frames (2.23s) — below min_shot_frames:75 in the blueprint preset, though render_stdout says min_shot:60f. Cannot verify which bound is live without the preset/edit yaml and the metrics file.
- Auto-zoom head-size 'matching' is contradicted by geometry: 303px vs 217px on-screen heads is a 1.40x size mismatch, not a match. The 1.146 'zoom ratio' compares crop heights, not resulting head sizes — the report conflates the two.
- Layout decision uses per-subject 'Back of Head Margin: 48.6px' identically for both subjects in layout_decision_c1.txt — looks like a single constant applied twice, not a per-subject measurement.

### Blocking
- QC gate FAILED (6 FAILs). A FAIL is never waived. This render does not pass and cannot be accepted as a §1.3/§1.4 proof.
- Crops are a static frozen window across all 1200 frames (hold_fraction 1.0, identical box every frame) — §1.4 BUILD_CROPS requires quantize_path over track box centres. The panels are not tracked; the ASD 'live panels' claim is a fixed crop of the 4K plate.
- No speaker_timeline_c1.json and no channel-layout proof. Blueprint §1.2 ASD is unverified. audio_channels:1 directly contradicts the per-mic VAD design; the discrete-channel assumption is unproven.
- shots_metrics_c1.txt not in report dir despite being cited — §1.3 required metrics (switches/min, mean/min shot, cut-on-pause fraction) are UNVERIFIED.

### Carried (non-blocking)
- speech-to-noise WARN 7.2 dB (< 15 dB target) — inherited denoise item, Phase 4 territory, not blocking this stage but track it.
- Top CAM_A lead-room proposal: AG wants a new split-panel band [0.55,0.67] for CAM_A. That is a preset/rule change — requires Commander approval, do not self-apply.
- back-of-head min 2.0% root cause (Fenton at sensor edge x=0) is physically real — needs a design answer (accept edge, or reframe), carry to next round.

## Why the loop stopped (Commander must clear)

