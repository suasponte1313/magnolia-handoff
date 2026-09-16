# Review — loop 9 — Phase 3 §1.2 (ANALYZE_SPEAKERS) / §1.1 (ANALYZE_FACES)
**Verdict: BLOCK**  ·  ⛔ HUMAN GATE

### Verified from artifacts
- channel_layout.txt ffprobe confirms source is ONE pre-mixed stereo AAC stream (channels=2, channel_layout=stereo), NOT two discrete mono mics — and astats shows Ch1 RMS -30.4dB vs Ch2 RMS -45.1dB, i.e. Ch2 is ~15dB down and not an independent speaker mic. AG's speaker.py line 107 correctly notes discrete per-channel energy is NOT used. Foundational input assumption now PROVEN: no per-mic VAD is possible.
- preset_weights_grep.txt + confidence_grep.txt confirm fusion weights (0.55/0.45), median-filter window (9), thresholds are loaded from presets/edit/magnolia_interview.yaml, not hardcoded literals.
- confidence_dist.txt shows no suspicious perfection: speaker confidences span 0.500–0.943, eq1.0=0; face confidences 0.000–0.826. Mouth motion max 0.8685/0.9091, eq1.0=0. Real, noisy measurement.
- fill_detection_gaps code holds last-valid, never centre-falls-back, raises on empty — matches spec §1.1. Face continuity hold behavior confirmed in face_tracks_c1.json boxes (frames 79–84 identical to 78).
- speaker_qc_*.json values match speaker_timeline_*.json headers exactly (c1: 0.424/26/180/0.746; c2: 0.5633/18/48/0.757; c3: 0.5202/7/7/0.763). QC sidecars are real.

### Unverified / suspicious
- THE BLOCKER: agreement_rate is 0.424 (c1), 0.5633 (c2), 0.5202 (c3) — ALL flagged=true, ALL far below the 0.8 threshold the blueprint §1.2 sets ('if below 0.8, flag the clip'). AG's report frames this as acceptable ('genuinely two speaker turns'). It is not acceptable — it is the stage failing its own gate on every clip.
- AG's diarization-degeneracy defense is CONTRADICTED by its own raw file. agreement_breakdown_c1.txt shows diar_label is 'spk_a' for frames 0–654 and 'spk_b' for ~662–1199 — i.e. for c1 the diarization is a near-constant single block, so 'agreement' is being measured against an almost-constant label. The mouth_label flips spk_a/spk_b dozens of times against that flat diarization. This isn't two clean turns agreeing; it's mouth-motion evidence disagreeing with a nearly-static diarization ~58% of the time.
- Mouth-motion evidence looks like noise, not speaker attribution. In agreement_breakdown_c1.txt during a diar 'spk_a' block (e.g. frames 8–21, 30–36, 56–64, 93–107...) mouth_label rapidly toggles spk_a/spk_b every few frames. mouth_motion_c1.txt confirms both speakers show comparable motion energy throughout — the lower-third frame-diff is picking up ambient/jaw motion on the listener, not identifying the talker.
- The 'both_frames: 0' across all three clips is suspicious given real 2-shot interview overlap. speech_threshold/overlap logic apparently never fires 'both' even where diar shows rapid turn-taking (c2/c3). Distribution of the overlap flag needs to be shown against the fusion code path.
- Diarization is the ONLY independent speaker signal left (per-mic VAD is impossible per channel_layout.txt). With mouth-motion this noisy, the fused timeline is effectively riding a single noisy source plus a near-static diarization label. switch_count=26 on c1 is being driven by mouth-motion thrash, not real speaker changes.

### Blocking
- agreement_rate < 0.8 on ALL THREE clips with flagged=true is a FAIL of the §1.2 gate. A WARN/FAIL that this stage is supposed to satisfy cannot be waived by a prose explanation. Either the fusion is wrong or the mouth-motion tiebreak is worthless on this footage — resolve it, don't relabel it.
- Now that channel_layout.txt PROVES single pre-mixed stereo (no discrete mics), the blueprint's primary evidence source (per-mic Silero VAD) is unavailable. The whole §1.2 evidence model must be re-derived around diarization-as-primary + a mouth-motion signal that actually discriminates speakers — or the low agreement is structural and permanent. This is a design decision above a mechanical fix.

### Carried (non-blocking)
- Step 0 gating items (0.1–0.6) from the blueprint were never confirmed landed in a prior accepted loop — audio constants removal, headroom/centre_x literals, verify_phase2 Check 1, LRA 8.2, ROI derivation. Do not let these vanish behind Phase 3 work.
- §1.1 face-track continuity/gap-fill is essentially verified and can be accepted once §1.2 is resolved.
- both_frames=0 overlap behavior still needs its distribution shown.

## Why this needs you
Two things need Commander judgment, not a mechanical re-run. (1) The proven input reality — one pre-mixed stereo stream, second channel 15dB down and not an independent mic — invalidates the blueprint's PRIMARY speaker-evidence source (per-mic VAD). The §1.2 architecture must be re-scoped around diarization + a mouth-motion signal that currently does not discriminate speakers (agreement 0.42–0.56, all clips flagged). (2) AG is presenting an all-clips-flagged FAIL as acceptable via a narrative that its own agreement_breakdown_c1.txt contradicts. Whether to re-derive the evidence model, accept a lower agreement target with justification, or change the capture is a design/product call. Do not advance to §1.3 BUILD_EDIT on a speaker timeline that fails its own gate on every clip.
