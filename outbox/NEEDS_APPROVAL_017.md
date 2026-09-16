# Review — loop 17 — Phase 3 Step 0 — channel-layout proof + LRA reconciliation (0.6)
**Verdict: REVISE**  ·  ⛔ HUMAN GATE

### Verified from artifacts
- Raw MOV audio (input/raw_part_2_Dallas1.mov) is ONE aac stream, channels=2, channel_layout=stereo — confirmed from ffprobe_raw_mov.txt. Source is a single pre-mixed stereo stream, not two discrete mic streams.
- Raw demuxed stereo WAV astats show the two channels are NOT identical and both are live: Ch1 RMS -33.42 dB / Peak -0.86, Ch2 RMS -32.82 dB / Peak -7.95. Second channel is not dead, but this is a stereo mix, not independent mics.
- Analysis WAV (output/master_audio_balanced.wav) is channels=1 mono (ffprobe_analysis_wav.txt) — the thing actually fed to analysis is a mono downmix, so per-mic VAD is physically impossible. AG's channel reality statement is correct and now proven.
- reel_1.qc.json 'loudness range' PASS measured 8.4 LU vs expected <= 11.0 LU — matches lra_qc_grep.txt (line 43) and lra_preset_grep.txt line 8 (lra_max_lu: 11.0). Preset raised from 8.0 to 11.0; QC reads the preset.
- reel_1.qc.json passed=true; subject centre X measured 33.1% (0.3306) PASS, head clipping 0% both sides PASS.

### Unverified / suspicious
- Item 1.c astats on the analysis WAV shows Ch1 Peak -1.000001 / RMS -26.115172 printed twice with IDENTICAL values and NO 'Channel: 2' block — consistent with a mono file, but the peak pinned at exactly -1.000001 is a limiter ceiling, fine; still, the mono downmix means per-channel speaker separation is gone. This kills the blueprint §1.2 'per-mic VAD as primary'. AG re-specced fusion to diarization-primary + mouth-motion tiebreak — that respec is a design change that needs Derek's signoff, not AG's unilateral edit.
- 0.6 LRA reconciliation: the bound was moved from 5–8 to <=11.0 to make an 8.4 measurement PASS. This is the 'sweep to green' pattern — the fix was to widen the gate, not the audio. The reason cited (EBU R128 shortform practice) is plausible but the original blueprint explicitly said LRA 8.2 is 'outside 5–8; fix via 0.1 or propose a bound with a reason.' A proposed bound is allowed, but 8.4 vs an old target of 8.0 is marginal — accepting an 11.0 ceiling is a policy change requiring Derek.
- Carried item 1 (233 Hz): reel_1.qc.json STILL shows room resonance WARN '233 Hz +14.2 dB'. AG says preflight notch targets [486.3, 474.6, 416.0] but QC measures 233 Hz post-render. Blueprint 0.6 explicitly ordered `grep -rn "233" scripts/` and removal of every literal, and to fix the notch to the MEASURED frequency. AG has NOT provided the grep, has NOT shown the notch is measured-driven, and is deferring 233 Hz to Phase 4 — that is deferring a WARN the stage is supposed to fix.
- No Step 0 items 0.1–0.5 evidence in this report at all: no dsp.py grep showing REEL_CONFIGS gone, no headroom_pct/centre_x_pct literal grep, no verify_phase2.py Check 1 diff, no transcript re-transcribe result. This report only covers channel layout + LRA. Step 0 is not complete.
- reel_1.qc.json path is output/test_verify_manifest/... — good that it's a fresh render path, but no accompanying raw stdout of the render+QC run was included, so I cannot confirm this qc.json was produced by a fresh render vs. copied.

### Blocking
- Blueprint §1.2 respec (per-mic VAD primary → diarization-primary) is a foundational architecture change forced by the proven mono/stereo-mix reality. AG cannot unilaterally rewrite the fusion spec; Derek must ratify the new speaker-attribution design before ANALYZE_SPEAKERS is built.
- LRA ceiling widened to 11.0 to pass — requires Derek's approval as a standing delivery-preset change, not AG's call.

### Carried (non-blocking)
- 233 Hz room resonance WARN still present in reel_1.qc.json; owed: `grep -rn "233" scripts/` output AND proof the notch targets the QC-measured frequency, not a guessed set.
- Step 0 items 0.1 (REEL_CONFIGS removal), 0.2 (headroom_pct/centre_x_pct literals), 0.4 (transcript re-transcribe), 0.5 (verify_phase2 Check 1 real diff) — still unreported.
- Left/right balance WARN (90/10) and framing stability WARN (30px std) — deferred to §1.4 BUILD_CROPS; acceptable to carry ONLY if §1.4 actually resolves them.

## Why this needs you
Two items need the Commander's judgment: (1) the proven source is a single pre-mixed stereo stream downmixed to mono for analysis, which invalidates blueprint §1.2's 'per-mic VAD as primary' evidence source. AG's proposed respec (diarization-primary + mouth-motion tiebreak) is reasonable but is an architecture change to the Phase 3 spec and must be ratified before speaker code is built. (2) The LRA delivery bound was widened 8→11 LU to turn a marginal miss into a PASS — that is a standing-config change and a sweep-to-green risk. Both should be Derek's call. Recommend: approve the mono-reality-driven respec (the ffprobe proof is solid), and either accept the documented 11.0 LU bound with the R128 justification on record OR direct AG to bring the actual audio down instead of widening the gate.
