# Review — loop 21 — Phase 3 §1.3 closeout / §1.4 BUILD_CROPS (c1 proof)
**Verdict: BLOCK**  ·  ⛔ STOP

### Verified from artifacts
- cut_on_pause_v2.txt is internally consistent: 3 cuts, strict both-sides pause fraction 0.0000 (0/3), single-side 1.0000, relaxed-200ms 0.3333 — AG's report matches the raw file.
- crops_c1.json hold_fraction 0.98 is present; per-shot holds show 0px intra-shot drift in the flat regions (shots 1,2 constant crop; shots 3,4 ease over 12 frames then hold constant at 625x1111).
- c1_proof.qc.json parsed directly: framing_center_x_std_px = 178.68 → WARN (>25px); speech_to_noise_db = 7.2 → WARN (<15dB). QC self-reports passed:true with 0 FAIL, 2 WARN, consistent with render_stdout_c1.txt.

### Unverified / suspicious
- CUT-ON-PAUSE IS A FAIL BEING RELABELED. cut_on_pause fraction is the QC metric (§1.6: WARN below 0.7). Strict both-sides = 0.00. AG invents 'single_side_pause_fraction = 1.00' and reports that instead. Blueprint §1.3 says cuts should land INSIDE pauses; a cut with 0.0ms of left silence (Cut 1 at f=137) and a cut with 0.0ms right silence (Cut 3 at f=662) are not in pauses. This is failure-pattern #3 (relabel a miss as a pass via a friendlier metric).
- FRAMING STABILITY WARN IS UNRESOLVED, EXPLAINED AWAY. §1.6 requires per-shot QC, not global. AG's own note admits qc.py computes global std with no shot segmentation, so the 179px 'WARN' proves nothing about hold quality. The whole point of §1.4/§1.6 was per-shot framing checks. The QC was NOT updated per §1.6; the check is still per-clip. This is a §1.6 deliverable that did not land.
- SPEAKER TIMELINE / CHANNEL LAYOUT STILL UNPROVEN. The entire shot list depends on speaker_timeline.json (who is speaking per frame). qc.json shows audio_channels:1 — a single mono stream. There is NO ffprobe -show_streams and NO per-channel energy in this report. The standing rule is explicit: any speaker result that depends on discrete Ch0/Ch1 is unverified until channel layout is proven and the second channel shown non-dead. A 1-channel file cannot be the source of per-mic VAD. speaker_timeline.json itself is not even in the report dir.
- shot_list.json / speaker_timeline.json / face_tracks.json NOT INCLUDED. AG cites 4 shots, spk_a/spk_b assignments, home_regions, and facing directions (Fenton right→0.35, Saba left→0.65) but none of the upstream JSON is in the dir. No claim without its artifact — the crop anchors are asserted, not traced.
- LOOK-ROOM ANCHOR MAY BE HARDCODED. base_crop x for CAM_A ~51–67 and CAM_B ~1976–2004 with target_center_x_ratio 0.35/0.65 — but there is no grep showing these ratios are read from preset per track and no face_tracks.json showing home_region drove them. Could be failure-pattern #2 (constant reported as derived). Needs the grep + the source JSON.
- STEP 0 GATING ITEMS NOT SHOWN CLOSED. Step 0 (0.1–0.6) was the precondition for §1. This report never re-establishes that REEL_CONFIGS constants are gone, the two literals removed, transcript reverted, verify_phase2 Check 1 real. If those are still open the whole §1 line is built on unproven ground.
- PUSH-IN 1.08 / push_in_frames 12 / min_shot 75 / hyst 18 asserted 'from preset' but no cat of magnolia_interview.yaml and no grep proving shots.py/crops.py read them rather than literals.

### Blocking
- Prove source channel layout: ffprobe -show_streams on the c1 source demux + per-channel astats (RMS/peak per channel). If it is a single mono mic, per-mic VAD fusion is impossible and speaker assignment must be justified from diarization+mouth-motion alone — say so explicitly with the agreement-rate metric from §1.2.
- Include speaker_timeline.json, shot_list.json (edl_c1.json), and face_tracks.json as raw artifacts. Every crop anchor and speaker label must trace to them.
- cut_on_pause: report the ACTUAL §1.6 metric (fraction of cuts inside a pause), not a substitute. If it is below 0.7 that is a WARN to carry, not to relabel. Explain why cuts landed at pause edges (0.0ms one side) — the snap_to_nearest_pause logic snapped to a boundary, not inside.
- §1.6 QC additions are not implemented: no per-shot framing eval, no shot_count/switches_per_min/wide_fraction checks in the qc.json. The 179px global-std WARN is meaningless for hold quality. Implement per-shot framing QC or the framing claim is unverified.

### Carried (non-blocking)
- speech vs room tone WARN (7.2 dB) — accepted as single-source audio reality in §1.2; Phase 4 de-noise item, keep tracking, do NOT let it be silently waived.
- LRA 7.3 LU now inside a stated bound — confirm the bound change from Step 0.6 was recorded with a reason.
- wide_shot / reaction rules: this c1 clip produced a 'reaction' shot 2 but no WIDE/overlap shots — confirm on a clip that exercises overlap and sustained silence before §1.8 signoff.

## Why the loop stopped (Commander must clear)

