# Review — loop 19 — Phase 3 §1.1/§1.2/§1.3 (c1 proof)
**Verdict: BLOCK**  ·  ⛔ STOP

### Verified from artifacts
- Channel layout PROVEN from raw: ffprobe_streams.txt shows 1 audio stream, 2ch stereo @48kHz; channel_energy.txt astats shows Ch1 RMS -37.16 dB and Ch2 RMS -29.58 dB (neither dead, distinct crest/zero-crossing stats). Source metadata = iPhone 17 Pro single-device capture → discrete per-mic isolation genuinely unavailable. §1.2 pivot to mono-VAD + diarization is justified. The long-open channel assumption is resolved.
- ROI literal 2109 is NOT hardcoded: roi_grep.txt is 0 bytes (grep miss). home_region_code.txt shows home_region derived via connectedComponentsWithStats + margin_px (rx1 = bl - 250); spk_b (2109,825,599,676) is a derived output in face_tracks_c1.json.
- Step 0 literals gone: step0_grep.txt (10.8|35.1|233|frame[900:1500]) is 0 bytes. 233 now appears only as a MEASURED resonance (233 Hz +14.2 dB) in verify_stdout Check 1 QC, and c1 measures different resonances (575/393 Hz) — consistent with real, noisy measurement.
- Face confidence is noisy real data (0.000–0.826 in face_tracks_c1.json), not a flat 1.000 placeholder.
- Shot list conserves frames: edl_c1.json 137+75+450+538 = 1200. switches_per_min 4.5 (3 cuts/40s), min shot 75f = floor, mean 10.0s — all recomputed and match shots_metrics_c1.txt.
- cut_on_pause 3/3 verified from cut_boundaries_c1.txt cross-checked against speaker_timeline_c1.json: cut@137 (136 spk_a→137 None), cut@212 (207–217 None), cut@662 (657–661 None→662 spk_b).
- verify_phase2 Check 1 executes a real render from manifest (1773 frames + live QC gate), Checks 2&3 run real TRANSCRIBE/ANALYZE_AUDIO with a mid-run SIGKILL and prove cache reuse.

### Unverified / suspicious
- BLOCKER: speaker_timeline_c1.json agreement_rate = 0.4967, which is below the blueprint §1.2 gate of 0.8, yet "flagged": false. In a 2-speaker clip 0.497 is chance — the mouth-motion tiebreak agrees with diarization no better than a coin flip, i.e. the visual evidence source contributes nothing. The self-check that is supposed to catch exactly this is disabled, and report.md reframes it as 'raw agreement between mouth motion and diarization' while asserting 'all requirements fulfilled'. Failure pattern #3 (miss relabeled as pass).
- Confidence floor: speaker_timeline_c1.json shows the exact value 0.700 in hundreds of frames (long runs whenever diarization sets the speaker). mean_confidence is therefore ~0.72 mostly composed of a 0.700 constant. Need the assignment line — is 0.7 a hardcoded default masquerading as fused confidence?
- Claim/artifact mismatch: report.md §3 states mean_confidence 0.706; speaker_timeline_c1.json states 0.724. The file wins; the prose is wrong.
- verify_stdout Check 1 diff shows EVERY field exactly equal (reference == fresh: lufs -14.60/-14.60, tp -1.80/-1.80, lra 8.40/8.40, s/n 16.60/16.60, center_x 0.33/0.33, head_top 0.20/0.20) despite non-zero tolerance columns. Exact-zero diff (failure pattern #5) — could be true determinism or a file diffed against itself. The two qc.json files were not included.
- speech-vs-room-tone 7.2 dB WARN on c1 (target ≥15) vs 16.6 dB on reel 1 — c1 audio is noticeably worse post-DSP; carry.
- framing stability 58px std WARN (framing_center_x_std_px 57.76) — un-quantized crop path; AG defers to §1.4 BUILD_CROPS (legitimately not-yet-built, not inherited-waived).

### Blocking
- agreement_rate 0.4967 is below the 0.8 gate but flagged=false. The flag logic is wrong AND the underlying active-speaker fusion is at chance (visual tiebreak useless). Multi-cam §1.3 was built on a timeline that should be flagged. Fix the flag, then fix/justify the mouth-motion signal so agreement is a real measurement — do not proceed to §1.4 on a chance-level speaker timeline.

### Carried (non-blocking)
- speech-vs-room-tone 7.2 dB WARN on c1 — Phase 4 audio rescue territory; track.
- framing stability 58px WARN — must be resolved by §1.4 BUILD_CROPS quantizer.
- Confirm 0.700 is not a hardcoded confidence floor.
- Provide reference + fresh qc.json for Check 1 to prove the diff is two distinct files.

## Why the loop stopped (Commander must clear)

