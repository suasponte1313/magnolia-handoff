# Review — loop 16 — Phase 3 Step 0 (gating fixes) + Item 1 mouth/diar reality
**Verdict: BLOCK**

### Verified from artifacts
- 0.1 — DSP derivation is real: dsp_derivation.txt shows gate_threshold_db = speech_median + offset, gate_ratio/range from measured_snr, comp_threshold from speech_median, limiter_ceiling from delivery true peak. Derived values (gate -27.0, ratio 2.9, range -15.7, comp -28.5, limiter -1.0) print in verify_phase2_stdout.txt render log. No per-clip audio literals shown.
- 0.2/0.6 — forbidden_literals_grep.txt for '10.8|35.1|233|frame[900:1500' in scripts/ returned EMPTY (exit 1). Those literals are gone from scripts/.
- 0.3 — qc.py has connectedComponentsWithStats at line 248 (qc_connected_components.txt). Measured framing_subject_center_x = 0.3305 in reel_1.qc.json — real ~33%, matches the wood-wall correction, PASS in 30-70 band.
- 0.3 — home_region is dynamic per clip: render log shows 'Dynamic home_region: (14, 911, 608, 638)' computed from first-frame detection, not a hardcoded ROI.
- 0.4 — 'body' transcribed unassisted from audio (transcript_body_grep.txt: seg 11 prob 0.99895, seg 39 prob 0.99582), vocabulary initial_prompt loaded from presets/captions/vocabulary.txt. No hand-patch.
- 0.5 — verify_phase2.py Check 1 does a real render from manifest to fresh path, runs QC, diffs 6 fields within tolerance, all PASS. Checks 2 & 3 exercise real TRANSCRIBE/ANALYZE_AUDIO with mid-run kill and cache reuse.
- Item 1 — mouth_vs_diar_c1.txt is real, noisy per-frame data. Counts recompute correctly: on the 491 disagreement frames, fused followed diarization on 490 and mouth on 0. Mouth motion is inert; timeline is diarization-driven. This is an honest walk-back of the earlier suspicious 0.999 'agreement.'

### Unverified / suspicious
- FOUNDATIONAL: audio_profile.json shows channel_count=1, a single mono channel (channel_index 0 only), correlation_matrix [[1.0]]. This is the pre-mixed single-stream case. The blueprint §1.2 makes Per-mic VAD the PRIMARY speaker evidence and explicitly requires independent channels 'verify at ingest.' There are NOT two independent mic channels here. AG's report prose asserts 'Ch2 dead at -45.1 dB' but NO ffprobe -show_streams and NO per-channel astats artifact is in the report dir to prove the source layout OR that second-channel energy. The -45.1 dB number has no artifact behind it — UNVERIFIED claim.
- LRA discrepancy: reel_1.qc.json measurements say lra 8.4 (QC PASS, expected <=11.0), but the render stdout says 'LRA: 8.5 LU (Target: 5-8 LU)' — outside the 5-8 target band cited in the render log itself. Step 0.6 explicitly asked to fix LRA into 5-8 or propose a bound with reason. AG did neither; it silently relies on the QC gate's looser <=11.0 while the DSP log flags its own target miss. Unreconciled.
- diar_winner column in mouth_vs_diar_c1.txt is single-speaker-dominant per long run (spk_a for frames 0-654, then spk_b) — consistent with transcript segment diarization, but no artifact ties this column back to dallas1_transcript.json segment boundaries. Plausible, not independently checked.
- room resonance still WARN at 233 Hz +14.2 dB in reel_1.qc.json — this is the measured QC value (fine to appear), but 0.6 asked to remove the 233 literal from scripts (done) AND the notch chain measures 486/474/416 from audio_profile while QC still flags 233. The DSP is not notching the frequency QC complains about. Carried, non-blocking, but note the mismatch.

### Blocking
- Prove the channel layout of the actual source with raw ffprobe -show_streams AND per-channel energy (astats) before any Phase 3 speaker architecture is built on 'per-mic VAD primary.' audio_profile.json says channel_count=1. If the master truly is a single pre-mixed stream, §1.2's primary evidence source does not exist and the fusion design must be re-specified around diarization-primary. The '-45.1 dB dead Ch2' claim needs its artifact — right now it contradicts the channel_count=1 profile.
- Reconcile LRA: render log says 8.5 LU vs its own 5-8 target; QC says 8.4 PASS vs <=11.0. Either fix the DSP to land in 5-8 or formally propose the 11.0 bound with a reason (per 0.6). Do not leave two conflicting targets.

### Carried (non-blocking)
- 233 Hz room resonance WARN persists while notch chain targets 486/474/416 — DSP is not addressing the frequency QC flags.
- left/right balance WARN (90/10) and framing stability WARN (30px std vs <25) still open — Phase 3 BUILD_CROPS quantize is expected to fix stability; verify it actually does, don't inherit.
- objc AVFFrameReceiver/AVFAudioReceiver duplicate-class dylib warning in the venv — cosmetic but could cause 'mysterious crashes'; track.

## Next prompt for AG
```
Step 0 is mostly verified — DSP derivation, dynamic home_region, connectedComponents patch, subject_center_x 33.1%, unassisted 'body', real verify_phase2 Check 1, and the honest mouth/diar walk-back all check out from artifacts. But you are BLOCKED from starting Phase 3 §1 on two items. Do NOT write any speaker.py / tracks.py code yet.

1. CHANNEL LAYOUT — prove it, raw only. Your report claims 'Ch2 dead at -45.1 dB' but audio_profile.json says channel_count=1, correlation [[1.0]]. Those contradict. Paste, no tables, no prose:
   a) ffprobe -v error -show_streams -select_streams a input/raw_part_2_Dallas1.mov  (full raw)
   b) ffprobe of the demuxed WAV actually fed to analysis.
   c) ffmpeg astats per channel: ffmpeg -i <that wav> -af astats=metadata=1:reset=0 -f null - 2>&1 | grep -E 'Channel|RMS_level|Peak_level'  (raw)
   Then state in one line: is the master ONE pre-mixed mono/stereo stream, or TWO independent mic channels? If it is one stream, §1.2's 'per-mic VAD = primary' does not exist and you will re-spec fusion as diarization-primary with mouth-motion tiebreak only. Do not proceed on an unproven channel assumption.

2. LRA — reconcile. Render log says 8.5 LU against a 5-8 target it prints itself; QC says 8.4 PASS against <=11.0. Pick one: either adjust the DSP formula/preset to land 5-8 and show the new audio_profile-derived number, OR propose the <=11.0 bound in presets with a one-line reason. Show the preset line (grep) and the QC value from the fresh qc.json.

Carry forward (do not lose): 233 Hz WARN persists while notch targets 486/474/416 — note why. left/right 90/10 and 30px framing-stability WARNs remain open for Phase 3 crops to fix, prove it not inherit it.

Write to handoff/inbox/report_17/. Raw ffprobe/astats/grep only. Touch DONE last. STOP at the next review boundary — do not start §1.1.
```