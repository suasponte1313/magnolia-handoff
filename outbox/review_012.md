# Review — loop 12 — Phase 3 §1.2 (ANALYZE_SPEAKERS) — Prompt 012 blocking items
**Verdict: REVISE**

### Verified from artifacts
- c1 boundary 655-678 'both' run is a genuine MISS, not real overlap: c1_boundary_650_682.txt shows every frame 655-678 fired on the 'mouth-AND' clause; at 662-678 diar_b=1.0/diar_a=0.0 with sb (0.5611-0.7562) dominating sa (0.1144-0.1791). Confirmed from raw.
- both_minscore_c1.txt enumerates 204 pre-smoothing 'both' frames; I confirmed header stats min=0.1113 (frame 30), max=0.4789 (frame 200), and every listed min_sa_sb < 0.50 — so the clip to 0.500 on the 'both' branch is explained, not a hardcoded placeholder. Confidence_assignment_source.txt line 469 shows np.clip(min(sa,sb),0.50,0.95).
- Fusion weights are preset-loaded, not literals: preset_weights_grep.txt shows weight_mouth 0.55 / weight_diarization 0.45 / speech_threshold 0.25 in magnolia_interview.yaml, and speaker.py:418-421 reads them via load_edit_preset.
- c1 agreement 0.8302 arithmetic checks out: c1_agreement_arithmetic.txt gives 836/1007 = 0.830189; denominator = vad_speech AND diar!=none. speaker_qc_c1.json agreement_rate=0.8302 matches.
- Confidence is not suspiciously flat: confidence_dist.txt shows eq1.0=0 across all face tracks and speaker timelines; distributions span 0.500-0.943. Non-'both' confidences are sigmoid-derived (speaker.py:474-482).
- fill_detection_gaps holds last-valid and raises rather than centre-falling-back (fill_detection_gaps_code.txt); face_continuity_c1.txt shows held frames, consistent with the code.
- c2/c3 turn-boundary agreement 87.3% and 100% from boundary_agreement.txt; c2 qc 0.8911, c3 qc 0.975 match their sidecars.

### Unverified / suspicious
- CHANNEL LAYOUT DISPROVES THE PER-MIC VAD SOURCE. channel_layout.txt ffprobe shows ONE aac stereo stream (channels=2, channel_layout=stereo), and astats shows Ch1 RMS -30.4 dB vs Ch2 RMS -45.1 dB, peak -9.0 vs -25.5 — this is a pre-mixed stereo camera-mic source, NOT two independent mono lav mics. The blueprint §1.2 lists 'Per-mic VAD energy (Silero on each demuxed channel)' as the PRIMARY evidence source, weight primary. There is no independent per-channel speaker energy here. speaker.py:107 even admits 'Discrete per-channel audio energy is NOT used for speaker attribution on pre-mixed'. So the fusion is actually mouth-motion (0.55) + diarization (0.45) only — VAD is a speech gate, not a speaker cue. The report never states this; it must be made explicit because the whole §1.2 design leaned on discrete channels.
- PROPOSED FIX NOT APPLIED. The report proposes removing/gating the mouth-AND clause but confidence_assignment_source.txt and fusion_explanation_frame_835.txt line 462-465 still contain the unconstrained (mouth_a>=0.20 and mouth_b>=0.20) clause. The miss is diagnosed but NOT fixed. The both_frames counts (c1=177) still include these false 'both' runs.
- overlap_threshold 0.08 cited in speaker.py:421 as fusion_cfg.get('overlap_threshold', 0.08) but preset_weights_grep.txt of magnolia_interview.yaml shows only weight_mouth/weight_diarization/speech_threshold under speaker_fusion — no overlap_threshold key. So 0.08 is currently a hardcoded default, not preset-backed. Add it to the yaml or show the grep line.
- speaker_qc.json both_frames for c1 = 177 but the raw pre-smoothing enumeration is 204; smoothing reduces it. That's internally consistent, but the 177 'both' frames are largely spurious given the mouth-AND defect above — the metric is reporting a real number over a broken decision rule.
- agreement_breakdown_c1.txt frames 824-842 and 893-951 show fused=spk_b while diar=spk_a yet agree=True — the 'agree' column appears to compare against a smoothed/segment diar label, not the per-frame diar_label shown. The 0.8302 arithmetic grep counts 'True.*True' rows; the column semantics need one line of explanation so the agreement number isn't silently inflated.

### Blocking
- Apply the mouth-AND fix (Option A or B) and re-emit speaker timelines + qc for c1/c2/c3. The diagnosis is accepted; a diagnosis with the defect still live in the source is not a completed stage.
- State explicitly, in the report and in code comments, that the source is single pre-mixed stereo (not discrete mics) and that per-mic VAD is NOT a speaker-attribution source here — only a speech gate. Re-label the §1.2 evidence table accordingly.
- Add overlap_threshold to presets/edit/magnolia_interview.yaml (or justify the bound) and grep-prove it; remove the 0.08 literal default.

### Carried (non-blocking)
- Explain the 'agree' column semantics in agreement_breakdown_c1.txt (per-frame vs segment diar) so the 0.8302 figure is auditable.
- Step 0 items from the blueprint remain unconfirmed in this loop's artifacts (headroom_pct/centre_x_pct literals, verify_phase2 Check 1 real diff, transcript re-transcribe, ROI first-frame derivation, LRA 8.2 bound). Do not let these lapse before §1 signoff.
- After the fix, re-run test suite; test_stdout.txt shows 100 passed but predates the clause change.

## Next prompt for AG
```
REVISE. Diagnosis accepted; the defect is still in the code. Do exactly this, then STOP at the review boundary.

1. APPLY the mouth-AND fix in reelcore/src/reelcore/edit/speaker.py. Use Option A (drop the unconstrained mouth-AND clause; require competitive composite scores) OR Option B (gate mouth-AND by diarization compatibility). Load overlap_threshold from the preset — do NOT leave 0.08 as a code default.
   - Add overlap_threshold to presets/edit/magnolia_interview.yaml under speaker_fusion.
   - Paste RAW: grep -n 'overlap_threshold\|is_both\|mouth_a\|mouth_b' reelcore/src/reelcore/edit/speaker.py and grep -n 'speaker_fusion' -A6 presets/edit/magnolia_interview.yaml. No tables.

2. RE-RUN analyze_speakers for c1/c2/c3. Paste RAW the new speaker_qc.json (all three) and RAW the frames 650-682 dump for c1 in the same columnar format as c1_boundary_650_682.txt, showing is_both now False across the single-speaker turn. Confirm the 24-frame both-run at 655-678 is gone.

3. CHANNEL LAYOUT — state it plainly. In report.md write one sentence: the source is a single pre-mixed AAC stereo stream (ffprobe channels=2, channel_layout=stereo; astats Ch1 RMS -30.4dB vs Ch2 -45.1dB), therefore per-mic VAD is a SPEECH GATE only and NOT a speaker-attribution source; §1.2 evidence is mouth-motion(0.55)+diarization(0.45). Paste RAW the ffprobe -show_streams and astats you already have (channel_layout.txt) plus the speaker.py:107 comment line proving the code knows this.

4. EXPLAIN the 'agree' column in agreement_breakdown_c1.txt: is it per-frame diar_label or segment-level? Show the exact code line that computes it (grep -n). Re-confirm the new agreement_rate arithmetic (numerator/denominator) from the raw breakdown after the fix.

Do NOT start §1.3. Do NOT touch render/EDL. Raw output only — no reformatting qc.json into tables. STOP at the boundary and wait for prompt_013.
```