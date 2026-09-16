# Review — loop 11 — Phase 3 §1.2 (ANALYZE_SPEAKERS) — Prompt 011 blocking items
**Verdict: REVISE**

### Verified from artifacts
- Channel layout PROVEN: channel_layout.txt ffprobe shows 1 audio stream, channels=2, channel_layout=stereo — a single pre-mixed stereo stream, NOT two discrete mono mics. astats: Ch1 RMS -30.39dB, Ch2 RMS -45.09dB (Ch2 ~15dB down, ambient). AG correctly does NOT use per-mic VAD (speaker.py:107 comment confirms). The blueprint's primary evidence source is unavailable and AG adapted — this resolves the standing channel-layout assumption honestly.
- Frame-835 arithmetic verified from mouth_motion_c1.txt: mouth_a[835]=0.3079, mouth_b[835]=0.0180 (matches raw table line 835). score_a=0.55*0.3079=0.1693, score_b=0.45*1.0=0.4599 → spk_b. Weights 0.55/0.45 confirmed in preset_weights_grep.txt.
- Confidence is computed, not hardcoded: confidence_grep.txt shows lines 455/469/476/482 all use np.clip(...); confidence_dist.txt shows eq1.0=0 across all timelines and a real spread (c1 min0.500 max0.918 mean0.699). The 0.500 'both' values are genuine lower-bound clamps of min(sa,sb) which maxes at 0.4789.
- c1 agreement 0.8302 = 836/1007 arithmetic is internally consistent with the selection rule speech_mask & (diar_a XOR diar_b); speaker_qc_c1.json reports 0.8302, matches.
- fill_detection_gaps raises rather than centre-falls-back (code confirmed); face continuity holds-last-valid confirmed in face_continuity_c1.txt.
- 100 tests pass (test_stdout.txt).

### Unverified / suspicious
- The c1 boundary_agreement.txt is INTERNALLY CONTRADICTED by agreement_breakdown_c1.txt. boundary_agreement.txt shows frames 647-654 as fused=None/spk_a with diar=spk_a, and 655-677 as 'both'. But agreement_breakdown_c1.txt at those exact frames shows fused_label=silence(651), spk_a(652-654), both(655-678) AND diar_label='none' at 655-661 then spk_b at 662+. The two artifacts disagree on diar_label for the SAME frames (647-654: one says spk_a, other says spk_a — ok; but 662+ one says spk_b as diar, breakdown says diar spk_b too). More seriously: boundary_agreement.txt reports the C1 boundary at frame 662, but agreement_breakdown_c1.txt shows the fused timeline transitions spk_a→both→spk_b around 655-679 while diar 'none' gap sits 655-661. The 12.5% boundary agreement is being explained away as 'conversational overlap (both)' — that is the exact 'relabel a miss as a pass' pattern. A 'both' verdict spanning 24 frames at a clean single-turn diarization boundary (spk_a→spk_b) is a MISS, not overlap.
- mouth_motion_c1.txt frame 835 is labeled 'mouth_spk_a (Dr. Fenton)' but this is reel_c1 whose window is 945-985s where diarization (diarization_segments_c1.txt) has spk_a=interviewer, spk_b=Dr.Saba. Frame 835 → t=972.833s falls in spk_b's segment (967.08-973.52 spk_b). Fusion picked spk_b. Fine. BUT the report calls spk_a 'Dr. Fenton' — no artifact establishes the spk_a/spk_b → real-name mapping. Cosmetic, not blocking, but names are unsourced.
- both_frames count discrepancy: speaker_qc.json c1 both_frames=177, but the aggregate speaker_qc.json and speaker_timeline_c1.json both say 177 — consistent. However the raw 'both' count AG claims for the confidence proof is '204 raw both frames' (confidence_assignment_source.txt) vs 177 post-smoothing. AG did not show the raw-204 list; the min(sa,sb) span [0.1113,0.4789] is asserted with NO artifact enumerating those 204 values. UNVERIFIED — this is the 'clean summary without the raw file' pattern.
- c1 has 177 both_frames + a 24-frame 'both' block straddling the only real turn boundary. This suggests the is_both trigger (mouth_a≥0.20 AND mouth_b≥0.20) fires on cross-talk/listener motion far too readily. c1 switch_count=56 over 1200 frames is high. No cut-on-pause or shot metrics yet (that's §1.3) but the speaker timeline quality feeding it is questionable at boundaries.

### Blocking
- Resolve the c1 boundary behavior. A 24-frame 'both' block (655-678) sitting exactly on the single spk_a→spk_b diarization turn is being reported as legitimate overlap to excuse 12.5% boundary agreement. Prove it is real simultaneous speech, not a mis-fire: paste, for frames 655-678, the raw mouth_a, mouth_b, diar_a, diar_b, sa, sb, is_both-branch-taken, for c1 (you have mouth_motion_c1.txt — cross it with diar). If mouth_b is only elevated because of listener nodding/cross-bleed while diar says a single speaker, that is a miss and the is_both mouth threshold (0.20) must be reconsidered — not relabeled.
- Produce the raw artifact behind the '204 raw both frames, min(sa,sb) span 0.1113–0.4789' claim. Dump the actual per-frame min(sa,sb) for every raw 'both' frame in c1 to a file. No claim without its artifact.

### Carried (non-blocking)
- spk_a/spk_b → real-name (Dr. Saba / interviewer) mapping is unsourced; name it from catalog or drop the names from prose.
- Step 0 gating items (0.1–0.6 from PHASE3_BLUEPRINT) — confirm these landed before §1.3; §1.2 was allowed to proceed but Step 0 was gated ahead of §1.
- c1 switch_count=56 and both_frames=177 look high; watch whether §1.3 hysteresis/min_shot absorbs this or whether the speaker timeline needs tightening first.
- Channel-layout finding (pre-mixed stereo, dead-ish Ch2) is now PROVEN and should be recorded so Phase 4 multi-mic de-bleed does not assume discrete channels.

## Next prompt for AG
```
Two blocking items before §1.3. RAW only — no tables, no prose summaries.

1. c1 boundary at frame 662 shows 12.5% agreement because frames 655-678 are labeled 'both'. Prove that is real overlap, not a mouth-threshold mis-fire. For c1 frames 650-682 dump ONE file with columns: frame, mouth_a, mouth_b, diar_a, diar_b, sa, sb, is_both(True/False), which is_both sub-clause fired (mouth-AND / diar-AND / score-AND), raw_label. Pull mouth_a/mouth_b straight from mouth_motion_c1.txt so I can cross-check. If 'both' is firing only on the (mouth_a>=0.20 AND mouth_b>=0.20) clause while diarization shows a single clean speaker turn, that is a MISS — do not call it overlap; report it as such and propose a fix to the mouth is_both threshold or gating.

2. You asserted '204 raw both frames, min(sa,sb) span 0.1113-0.4789 mean 0.1875' with no artifact. Dump the actual per-frame min(sa,sb) for every raw (pre-smoothing) 'both' frame in c1 to both_minscore_c1.txt — one value per line with its frame index. 

Do NOT start §1.3. Stop at the review boundary after writing report_012 with these two files.
```