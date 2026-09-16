# Review — loop 15 — Phase 3 §1.2 — Channel reality + speaker agreement (Step 0 gating context)
**Verdict: REVISE**

### Verified from artifacts
- Channel layout: input/raw_part_2_Dallas1.mov has ONE pre-mixed AAC stereo stream (channel_layout.txt ffprobe: 1 audio stream, channels=2, stereo). Confirmed no discrete mic tracks.
- Per-channel energy: astats Ch1 RMS -30.39 dB, Ch2 RMS -45.09 dB — Ch2 is ~14.7 dB down. AG's claim Ch2 is dead/ambient is supported by the raw astats.
- No per-mic VAD attribution in code: grep of speaker.py confirms VAD runs on downmixed mono (-ac 1), diar_a/diar_b come from transcript segments only, no channel-energy speaker attribution. This is the honest position given the source.
- Confidence is computed, not hardcoded: confidence_assignment_source.txt shows sigmoid/np.clip formulas; confidence_dist.txt shows spread (speaker_c1 min 0.500 max 0.918 mean 0.742, eq1.0=0). No flat-1.000 field.
- both_frames=0 explained: both_minscore_c1.txt shows all 204 raw 'both' candidate frames have min(sa,sb) max=0.4789 < 0.50 threshold; after window-9 modal smoothing none survive as 'both'. Consistent with speaker_qc.json both_frames=0.
- fill_detection_gaps holds last valid, never centre-fallback, raises on total failure (fill_detection_gaps_code.txt) — matches blueprint §1.1.
- Fusion weights from preset, not literals: preset_weights_grep.txt shows weight_mouth 0.55 / weight_diarization 0.45 / speech_threshold 0.25 loaded via load_edit_preset.
- 100 unit tests pass (test_stdout.txt).

### Unverified / suspicious
- THE AGREEMENT RATE IS CIRCULAR. c1_agreement_arithmetic.txt states the fused decision is score_a=w_mouth*mouth+w_diar*diar with w_diar=0.45, and the agreement metric compares fused_label vs diar_label. fusion_explanation_frame_835.txt proves diarization is a +0.45 binary prior that mouth motion (weighted 0.55) can rarely overcome (needs mouth>0.836). So the 'active speaker' is essentially the diarization label, and 0.999/1.0 agreement is the timeline agreeing with its own dominant input. This is the 'file diffed against itself' failure pattern. The single disagreeing frame in c1 (659-661, per agreement_breakdown) is only in a diar='none' gap where mouth breaks the tie. This metric does NOT validate active-speaker correctness.
- mouth_raw disagrees with fused on a LARGE fraction of speech frames (agreement_breakdown_c1.txt: mouth_raw flips spk_a/spk_b constantly while fused stays locked to diar). The tiebreak evidence source is being overridden ~everywhere diar is present — the report never quantifies how often mouth and diar disagree, which is the real measure of whether mouth motion adds anything.
- vad_diar_speech_agreement ratio 0.839 (numerator 1007/1200) is a SPEECH-vs-SILENCE agreement, not speaker agreement — fine, but the report leans on it as if meaningful; the blueprint's required 'agreement rate between VAD and diarization, flag if <0.8' is being satisfied by a near-tautological speech-gate comparison.
- c2/c3 agreement = 1.000 exactly: suspicious perfection. Given circularity above, 1.000 is expected but is not evidence of quality.
- Step 0 items 0.1-0.6 are RESTATED as CLOSED with no fresh artifacts this loop: no audio_profile.json, no reel_1.qc.json showing subject_center_x, no verify_phase2.py Check-1 diff output, no re-transcription 'body' result, no grep of scripts/ for the literals. These were the open blocking items and remain UNVERIFIED here.

### Blocking
- Report an honest speaker-attribution accuracy that is NOT circular: e.g. frames where mouth-motion and diarization DISAGREE, and which one matches ground truth. The current 0.999/1.0 measures the timeline against its own dominant input and cannot be accepted as validating ASD.
- Quantify mouth-vs-diar disagreement rate per clip from the raw mouth_motion + diar arrays. If mouth never overrides diar, state that the mouth tiebreak is effectively inert on this source and justify keeping it.
- Step 0 (0.1-0.6) artifacts must actually appear in a report dir before §1.2 is signed off — they are asserted CLOSED with zero supporting files this loop.

### Carried (non-blocking)
- home_region values (c1 [2109,825,599,676], c2 [2104,857,598,662], c3 [2108,836,602,666]) are per-clip derived and stored — looks right, but confirm they come from first-frame full detection, not a literal, when tracks.py is reviewed.
- confidence floor of 0.50 via np.clip everywhere — acceptable as a floor but means 'confidence' is never below 0.5; note this when QC thresholds are set.
- single-mono-channel reality means Phase 4 multi-mic cross-talk de-bleed (keyed to speaker_timeline) has NO independent channel to attenuate — flag now for Commander before Phase 4 is scoped.

## Next prompt for AG
```
STOP after this. Do NOT advance to BUILD_EDIT. Two things, raw output only, no tables, no prose summaries substituting for files.

1. Prove the speaker attribution is not circular. From the raw mouth_motion_c1.txt and the diar arrays you already have, compute and paste a raw per-frame file for c1 restricted to SPEECH frames where diar_a XOR diar_b is set: columns frame, mouth_winner (argmax of mouth_a/mouth_b), diar_winner, fused_winner, agree_mouth_vs_diar. Then paste the summary counts: N_speech_diar_frames, N where mouth_winner==diar_winner, N where mouth_winner!=diar_winner, and of those disagreements, N where fused followed diar vs N where fused followed mouth. This is the real number. I expect mouth to disagree with diar on a large fraction and fused to follow diar almost always — if so, say plainly that mouth motion is effectively inert on this pre-mixed source and the 0.999 agreement is the timeline echoing its own diarization input. Do not report 0.999 as an accuracy again.

2. Land the Step 0 artifacts you marked CLOSED. Paste raw, each into report_16:
   - `cat output/audio_profile.json` and the derived gate/comp/limiter values from dsp.py (grep the derivation lines).
   - `cat output/reel_1.qc.json` showing subject_center_x, and `grep -n connectedComponentsWithStats reelcore/src/reelcore/qc.py`.
   - Full stdout of `./venv/bin/python3 scripts/verify_phase2.py` showing the Check-1 fresh render + per-field QC diff.
   - The re-transcription result for the word 'body' (grep it out of output/dallas1_transcript.json) and confirm no hand-patch.
   - `grep -rn '10.8\|35.1\|233\|frame\[900:1500' scripts/` — must be empty.

Write report_16 with these files, touch DONE last, and STOP. No new stages.
```