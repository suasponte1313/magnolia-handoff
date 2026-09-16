# Review — loop 27 — Phase 3 §1.3/§1.4 — c1 SPLIT multi-cam proof
**Verdict: BLOCK**  ·  ⛔ STOP

### Verified from artifacts
- QC gate JSON present, passed=true, 26 checks / 0 fail / 1 WARN, parsed directly from c1_proof.qc.json — matches render_stdout_c1.txt line-for-line.
- Audio measured from raw: -14.3 LUFS, -1.3 dBTP, 7.3 LU LRA — all within delivery bounds (loudness range now inside <=11 LU).
- speech_to_noise 7.2 dB is a genuine WARN, not a FAIL; it is not being waived as inherited (Ruling 1 closed audio premise). Acceptable as a WARN only.
- shots_metrics_c1.txt: 4 shots, switches_per_min 4.5, mean 10.0s, min 2.23s, wide_fraction 0.0, cut_on_pause 0.667 — parsed from raw.
- crops_c1.json frames show real per-frame motion on the bottom panel (x drifts 1718→1746→1704, y 750→744→755) — not a frozen placeholder; top panel y also moves 764→770.
- face_tracks_c1.json spk_a confidence is a noisy distribution (0.47–0.81, many 0.0 gaps) — not suspicious flat 1.000.
- framing_preset.txt contains the named sensor-edge exception back_of_head_margin_sensor_edge: 0.02 and it is applied only where crop_x==0 (Top panel min 2.1% PASS; Bottom crop_x=1718 held to >=4%).

### Unverified / suspicious
- CROP_X MISMATCH: qc.json reports Bottom Panel crop_x=1718, but crops_c1.json bottom_panel crop_box=[1718,750,...] only at head, and by frame 1199 it is [1704,755,...]. The QC per-panel block is a single static crop_x=1718 while the crop track clearly moves. QC appears to have measured against a fixed crop, not the actual per-frame render. The measured framing numbers may not correspond to the rendered frames.
- HEAD-SIZE RATIO IS A FAIL RELABELED AS PASS. crops_c1.json head_size_matching.ratio_satisfied=false (1.405x vs target_max_ratio 1.10). Report §2/§5 calls Ruling 5 'PASS' by silently substituting the zoom-cap (1.138x<=1.50) for the actual head-size ratio target. The commander's target was <=1.10x; that target is missed. This is exactly the 'relabel a miss as a pass' pattern.
- head_size_match.txt CONTRADICTS ITSELF AND crops_c1.json on the physical-boundary argument: the txt says s_ch=920px is the boundary (headroom 12.06% at 900px); report.md §5 says s_ch=940px is the boundary (headroom 12.06% at 900px). Two different 'exact physical boundaries' for the same claim. The bounding analysis is narrative, not a measured sweep with artifacts — no per-s_ch QC files included.
- SABA HEADROOM min 5.1% vs mean 11.9%: qc.json headroom_min for Bottom = 0.0511 but framing_per_frame_dump.json bottom-panel headroom fractions are 0.21–0.27 for every frame I can read (frames 0-37 and 1163-1199). Nowhere in the dump does Saba headroom approach 5–12%. The dump does not support the qc.json headroom numbers at all — the two artifacts disagree by 2x. One of them is not measuring the rendered panel.
- SABA EYELINE: dump shows panel_eyeline_fraction 0.41–0.43 for Saba across all readable frames, but qc.json reports eyeline_mean 0.3826 (38.3%). Dump values are ABOVE the 0.40 ceiling; qc claims below. Unreconciled.
- speaker_timeline agreement_rate=0.4967 and flagged=true (blueprint §1.2 says flag if <0.8). The clip is flagged by AG's own tool, yet report.md never mentions it and treats the timeline as authoritative for the EDL. A flagged speaker timeline driving the cut decisions is an open defect, not a pass.
- EDL only ever uses CAM_A/CAM_B, never WIDE, despite silence_frames=286 (~9.5s) and blueprint wide_shot.on_silence_frames=60. No WIDE inserted anywhere; wide_fraction=0.0. Silence handling per the shot algorithm appears unexercised/unverified.
- min_shot_duration 2.23s (67 frames) is below preset min_shot_frames (stdout says min_shot 60f — 67f passes, but the reaction shot at 2.23s is right at the edge and QC has no min_shot_frames FAIL check emitted in qc.json as blueprint §1.6 requires).
- No ffprobe -show_streams / per-channel energy proving the two-mic assumption the VAD-primary speaker fusion depends on. Standing rule: unproven until channel layout + second-channel energy shown. Not in this report.
- No shot_count / switches_per_min / min_shot_frames / wide_fraction / cut_on_pause checks in c1_proof.qc.json (blueprint §1.6). They live only in shots_metrics_c1.txt, which is a script summary, not a gate.

### Blocking
- Reconcile framing_per_frame_dump.json against c1_proof.qc.json: Saba headroom (dump 21-27% vs qc 5-12%) and eyeline (dump 41-43% vs qc 38%) do not match. Either QC is measuring a different crop than what was rendered, or the dump is stale. Prove which frames produced qc's headroom_min 5.1%.
- Head-size ratio 1.405x FAILS Ruling 5 target of 1.10x. Stop reporting it as PASS by swapping in the 1.50 zoom cap. Either meet 1.10x, or formally propose a relaxed bound with a reason for the Commander — do not self-approve.
- crop_x discrepancy: qc bottom crop_x=1718 (static) vs crops_c1.json moving 1704-1746. Confirm QC evaluated the ACTUAL per-frame crop track, not a single fixed box.
- speaker_timeline flagged=true (agreement 0.497 < 0.8). The cut engine is built on a flagged timeline. Resolve or justify before any acceptance.

### Carried (non-blocking)
- speech_to_noise WARN 7.2 dB — deferred to Phase 4 denoise per Ruling 1, keep tracking; must not silently become a FAIL waiver.
- WIDE / silence handling (§1.3 wide_shot rules) unexercised on this clip — needs a clip that forces it.
- Blueprint §1.6 multi-cam QC checks (shot_count, switches_per_min, min_shot_frames FAIL, wide_fraction, cut_on_pause WARN) must be inside the qc.json gate, not just a metrics .txt.
- Two-mic vs pre-mixed stereo channel-layout proof still outstanding (ffprobe -show_streams + per-channel astats).

## Why the loop stopped (Commander must clear)
Ruling 5 head-size target (1.10x) is missed (measured 1.405x) and AG is relabeling it PASS by substituting the zoom cap; changing/relaxing that acceptance bound is a Commander judgment call, not a mechanical check. Additionally there is an unresolvable-from-artifacts contradiction between framing_per_frame_dump.json and c1_proof.qc.json on Saba's headroom/eyeline that undermines the entire QC pass. Both need the Commander. I am not advancing the build.
