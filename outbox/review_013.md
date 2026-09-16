# Review — loop 13 — Phase 3 §1.2 — ANALYZE_SPEAKERS (channel proof + both-frame fix + agreement arithmetic)
**Verdict: REVISE**

### Verified from artifacts
- Channel layout is single pre-mixed AAC stereo, NOT two discrete mono mics: channel_layout.txt ffprobe shows channels=2, channel_layout=stereo, one audio stream (index 1). astats confirms Ch1 RMS -30.4dB, Ch2 RMS -45.1dB — Ch2 is ~15dB lower and clearly not an independent second mic. AG's Option-A decision (mouth 0.55 + diar 0.45, per-mic VAD demoted to a speech gate only) is the correct and honest conclusion from this evidence. This resolves the standing foundational assumption.
- speaker.py:107-109 documents that per-channel audio is NOT used for attribution — matches the fusion code.
- Fusion weights are preset-driven, not hardcoded: preset_weights_grep.txt + fusion_explanation shows w_mouth/w_diar/threshold/overlap loaded from presets/edit/magnolia_interview.yaml via load_edit_preset (speaker.py:414-420). Hand-worked frame 835 arithmetic (0.55*0.3079 + 0.45*0.0 = 0.1693 vs 0.55*0.018 + 0.45*1.0 = 0.4599 -> spk_b) checks out against mouth_motion_c1.txt values.
- Confidence is computed, not a flat literal: confidence_assignment_source.txt + confidence_dist.txt show speaker_c1 min=0.500 max=0.918 mean=0.742, eq1.0=0; face tracks eq1.0=0/1200. Sigmoid-of-margin and sil_certainty branches are real. No suspicious flat 1.000.
- fill_detection_gaps (quantize.py) holds last valid, raises on total failure, never centre-falls-back — matches blueprint §1.1.
- 100 unit tests pass (test_stdout.txt).
- c1 both-frames run at 655-678 is eliminated in the fused timeline: c1_boundary_650_682.txt shows is_both=False on every frame 650-682; speaker_qc_c1.json reports both_frames=0.

### Unverified / suspicious
- AGREEMENT RATE IS INTERNALLY CONTRADICTED. report.md and speaker_qc.json/speaker_qc_c1.json assert c1 agreement_rate=0.999 (1006/1007). But AG's own artifact c1_agreement_arithmetic.txt computes 836/1007 = 0.8302 for the SAME reel with the SAME denominator (1007). Two different numerators (1006 vs 836) for the identical evaluated set cannot both be true. The 0.999 in the shipped qc.json is the number that feeds 'flagged=false'; the 0.83 is what a direct grep of agreement_breakdown_c1.txt supports.
- agreement_breakdown_c1.txt DOES NOT SUPPORT 0.999. In that raw file, fused_label is 'spk_a' for essentially all of frames 0-654 while diar_label flips to 'spk_b' for large stretches (e.g. 7-23, 32-37, 53-65, 93-107, 163-171, 175-209, 227-245, 252-258, 261-280, 283-320, 329-343, 348-359, 375-419, 423-439, 452-454, 480-484, 488, 491-494, 499-504, 516-543, 559-563, 579-588, 593-607, 618-621) yet 'agree' is marked True on every one of those rows. That is the same false-True mislabel flagged in Review 12. AG's Item-4 'column-order misreading' explanation is refuted by AG's OWN file: at frame 7 fused=spk_a, diar=spk_a per this file — but the diarization SEGMENTS artifact (diarization_segments_c1.txt) shows the reel window is spk_a 943-966s then spk_b 967s+, i.e. frame-level diar_b never legitimately equals spk_a's fused label for hundreds of these frames. The 'agree' column is computing True from something other than fused==diar, so 0.999 is not a real agreement measurement.
- boundary_agreement.txt (AG's own artifact) reports c1 turn-boundary agreement of 3/24 = 12.5% at the single detected boundary (frame 662), while claiming c2=87.3% and c3=100%. A 12.5% boundary agreement on c1 is flatly inconsistent with a 99.9% global agreement on c1 and points to the fused c1 timeline being wrong through the spk_a->spk_b transition (fused holds spk_a far too long: frames 655-661 fused=spk_a while diar has already gone silent/none and mouth_b dominates).
- c1 fused timeline looks substantively wrong, not just mislabeled. speaker_timeline_c1.json holds spk_a from frame 0 through ~658 despite diarization_segments showing spk_b turns and mouth_motion_c1.txt showing mouth_b > mouth_a across long stretches (e.g. 16-23, 80-86, 165-169, 197-203, 370-375, 697-704). The mouth+diar fusion with binary diar as a +0.45 prior is pinning to whichever speaker the transcript labels — and on c1 the transcript window (943-966 all spk_a) makes fused=spk_a nearly monolithic, which is why 'agreement' looks perfect while boundary agreement collapses. This is the classic 'relabel/looks-perfect' pattern.
- agreement_rate is what drives flagged=false. Blueprint §1.2 says flag the clip if agreement < 0.8. If the real c1 number is 0.83 (AG's arithmetic file) or effectively ~12.5% at the boundary, the clip may need flagging — the 0.999 masks that.

### Blocking
- Reconcile c1 agreement_rate: 0.999 (qc.json) vs 0.830 (c1_agreement_arithmetic.txt) — both cannot be right. Produce the exact code that computes the number written to speaker_qc_c1.json and a raw recomputation from agreement_breakdown_c1.txt.
- The 'agree' column in agreement_breakdown_c1.txt marks True on rows where fused_label != diar_label (e.g. frames 7-23 region and dozens more). Fix the agreement computation OR prove line-by-line that fused==diar on those rows. Until then agreement_rate is UNVERIFIED and flagged=false is not trusted.
- Explain the c1 boundary agreement of 12.5% and whether the c1 fused timeline holding spk_a from 0-658 is correct given diarization_segments_c1.txt and mouth_motion_c1.txt.

### Carried (non-blocking)
- Channel-layout foundational question is now RESOLVED (single stereo, Ch2 dead-ish) — carry this forward so Phase 4 de-bleed does not assume discrete mics.
- §1.1 ANALYZE_FACES artifacts (face_tracks, fill_detection_gaps, home_region derived per clip) look good but were not the focus of this loop — confirm formal acceptance when §1.2 lands.
- Still open from Step 0 / prior loops (not re-litigated here): 0.5 verify_phase2 Check1 real diff, transcript re-transcribe with vocabulary ('body'), centre_x measured, LRA 8.2 bound. Do not lose these before §1.3.
- Once agreement is trustworthy, need switches/min, mean/min shot duration, wide fraction, cut-on-pause fraction from BUILD_EDIT (§1.3) — not yet produced.

## Next prompt for AG
```
§1.2 is NOT accepted. Your own artifacts contradict each other on c1 agreement and I do not trust flagged=false. Channel-layout proof (single stereo, Ch2 dead) IS accepted — keep the mouth0.55+diar0.45 fusion.

Fix and re-report ONLY these, raw output, no tables, no prose summaries:

1. c1 agreement is reported two ways: speaker_qc_c1.json says 0.999 (1006/1007); your c1_agreement_arithmetic.txt says 0.8302 (836/1007). Same denominator, different numerator. Paste:
   - `sed -n '505,535p' reelcore/src/reelcore/edit/speaker.py` (the exact block that computes the number written to the qc sidecar).
   - A one-shot python recompute that loads speaker_timeline_c1.json and dallas1_transcript.json, and for every frame prints: frame, fused_speaker, diar_label_at_that_frame, vad_speech, and eq=(fused==diar). Then prints numerator, denominator, ratio. Redirect to agree_recompute_c1.txt. The ratio it prints must equal what goes in speaker_qc_c1.json or you fix the qc writer.

2. In agreement_breakdown_c1.txt, rows like frames 7-23, 53-65, 93-107, 175-209, 283-320, 375-419, 516-543 show fused_label=spk_a, diar_label=spk_b, agree=True. Either fix the agree computation so agree = (fused==diar), or paste the diar segment lookup for frame 7 and frame 300 proving diar really is spk_a there. Raw.

3. Your boundary_agreement.txt says c1 boundary agreement = 3/24 = 12.5% at frame 662, while the fused c1 timeline holds spk_a from frame 0 to ~658. Given diarization_segments_c1.txt (spk_a 943-966s, spk_b 967s+) and mouth_motion_c1.txt (mouth_b > mouth_a across many stretches before 655), state plainly: is fused=spk_a for frames 0-654 CORRECT? If yes, prove it from mouth+diar per frame. If no, fix the fusion and re-emit speaker_timeline_c1.json + speaker_qc_c1.json.

4. After the real agreement number is known, re-evaluate flagged per blueprint §1.2 (<0.8 -> flag). Report the corrected flagged for c1/c2/c3.

Write everything to handoff/inbox/report_14/, copy the raw artifacts, DONE last. STOP at the review boundary. Do not start §1.3 BUILD_EDIT.
```