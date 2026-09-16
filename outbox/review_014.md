# Review — loop 14 — Phase 3 §1.2 (ANALYZE_SPEAKERS)
**Verdict: REVISE**

### Verified from artifacts
- c1 agreement arithmetic 1006/1007=0.999007 confirmed by parsing agree_recompute_c1.txt SUMMARY and counting the one mismatch at frame 651 (vad=True, fused=silence, diar=spk_a) in agreement_breakdown_c1.txt — matches speaker_qc_c1.json.
- diar_label is genuinely spk_a at frames 7 and 300: diarization_segments_c1.txt shows 943.16-948.64 spk_a (frame7 t=945.2) and 953.62-960.74 spk_a (frame300 t=955.0).
- Column identity in agreement_breakdown_c1.txt confirmed: col5 mouth_raw diverges to spk_b during listener motion while col4 diar_label stays spk_a and agree compares col3==col4.
- Confidence is computed, not a flat literal: confidence_dist.txt shows speaker_c1 min0.500/max0.918/mean0.742, eq1.0=0; assignment lines 454/469/475/481 use np.clip(...,0.50,0.95); the flat 0.500 blocks are explained (clip floor on both/silence).
- Fusion weights are preset-driven not hardcoded: preset_weights_grep.txt shows weight_mouth 0.55 / weight_diarization 0.45 loaded via load_edit_preset (speaker.py 415-419).
- fill_detection_gaps holds last valid and raises rather than centre-falling-back (fill_detection_gaps_code.txt).
- boundary_agreement.txt turn boundaries parse correctly (c1 19/20, c2 55/55, c3 53/53).

### Unverified / suspicious
- FOUNDATIONAL: channel_layout.txt proves ONE pre-mixed stereo AAC stream (1 audio stream, channel_layout=stereo), NOT two independent mono mics. astats shows Ch1 RMS -30.4 dB vs Ch2 RMS -45.1 dB (~15 dB down) — Ch2 is a room/ambient channel, not a second speaker mic. Per-mic VAD (the blueprint's PRIMARY evidence source) is therefore unavailable and was not used (speaker.py line107 comment).
- SUSPICIOUS PERFECTION / SELF-DIFF: with per-mic VAD absent, fusion is mouth(0.55)+diar(0.45). The +0.45 diar baseline dominates (fusion_explanation_frame_835 shows mouth alone needs 83.6% to overcome it), so fused_label ≈ diar_label whenever diar is present. Agreement rate is then measured against that same diarization — the 0.999/1.000/1.000 is diarization largely agreeing with itself, not independent corroboration. This inflates the gate.
- agreement denominator EXCLUDES frames where diar='none' (valid_eval requires XOR of diar_a/diar_b). c1: 1007 of 1200 frames evaluated; the 7 diar=none frames and 186 silence frames are dropped. The 'both' clause is never exercised on real data (both_frames=0 all three clips) — the overlap branch is untested against footage.
- speaker_c3 confidence hits 0.943 at frame203 and c2 0.926 at frame892 — high but within clip; not perfection, acceptable, but note c3 has only 6 silence frames / switch_count 7 (near-single-speaker), so its 1.000 is trivially easy.
- No independent ground truth for active-speaker correctness: every 'proof of correctness' (frames 0-654, 835) reasons FROM diarization, which is the same signal being agreed-with. No VAD-vs-diar agreement rate is reported as the blueprint §1.2 explicitly requires ('Report agreement rate between VAD and diarization; if below 0.8, flag').

### Blocking
- Channel layout resolved as pre-mixed stereo with a dead/ambient Ch2 — the blueprint's PRIMARY (per-mic VAD) evidence source does not exist for this footage. The §1.2 agreement metric as implemented compares fused output against the diarization that dominates it, so it cannot certify speaker attribution. AG must either (a) compute the blueprint-specified VAD-vs-diarization agreement using master-audio Silero VAD as an INDEPENDENT signal from diarization, or (b) get Commander sign-off to redefine the §1.2 metric given no discrete mics.

### Carried (non-blocking)
- Step 0 gating items (0.1-0.6) — no evidence in this report that REEL_CONFIGS constants, headroom_pct/centre_x_pct literals, verify_phase2 Check1, transcript re-transcribe, or LRA 8.2 were closed. Confirm before any §1.2 accept.
- 'both'/overlap branch has zero real-footage coverage (both_frames=0 x3) — needs a clip with genuine crosstalk or a synthetic integration case.
- face_tracks home_region derived per clip (c1 [2109,825,599,676]) — spot-check but not this loop's gate.

## Next prompt for AG
```
Do NOT start §1.3. Two things, raw output only, no tables, no prose summaries.

1. The agreement metric is circular. You measure fused_label vs diarization, but fused_label is dominated by the same diarization (+0.45 baseline). That is not independent corroboration. Produce the metric the blueprint §1.2 actually asks for: Silero VAD run on the MASTER mono audio as a per-frame speech/active signal, agreement measured against diarization as a SEPARATE source. Give me: the script lines that run Silero and derive per-speaker VAD energy WITHOUT reusing the transcript; and a raw per-frame file for c1 with columns frame, vad_active_speaker, diar_label, agree — plus numerator/denominator/ratio. If you cannot derive a per-speaker VAD signal because there is only one usable audio channel, say so in one line and STOP.

2. Confirm the channel reality in writing with the artifact already present: paste the ffprobe stream count and the astats Ch1 vs Ch2 RMS dB lines, and state explicitly whether any code path still assumes discrete Ch0/Ch1 mic attribution. grep -rn 'diar_a\|diar_b\|channel' reelcore/src/reelcore/edit/speaker.py and paste it.

3. Exercise the 'both'/overlap branch at least once: either a real slice with crosstalk or a synthetic frame set. Paste the raw frames where raw_decisions=='both' and the resulting confidence.

Also restate, one line each with the source filename, the status of Step 0 items 0.1-0.6 — those were never shown closed. STOP at this boundary.
```