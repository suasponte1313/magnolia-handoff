# Review — loop 18 — Phase 3 §1.3 BUILD_EDIT (c1 proof render)
**Verdict: BLOCK**  ·  ⛔ STOP

### Verified from artifacts
- Shot/frame arithmetic checks out from edl_c1.json + shots_metrics_c1.txt: 4 shots, 3 cuts, boundaries 137/212/659, frames sum 137+75+447+541=1200 = 40.0s @30fps; switches_per_min 4.5, mean_shot 10.0s, min_shot 75f are internally consistent.
- c1_proof.qc.json parsed directly: passed=true with 14 checks, 0 FAIL, but 2 WARN — 'speech vs room tone' 7.2 dB (target >=15) and 'framing stability' 57px std (target <25). AG's report 'passed all automated QC checks (0 failed)' omits both warnings.
- framing_subject_center_x 0.44910879… and framing_center_x_std_px 57.07 are noisy measured floats, not round literals — these read as genuinely measured, not hardcoded.
- speaker_timeline_c1.json frame accounting is consistent: spk_a 580 + spk_b 439 + silence 181 + both 0 = 1200.

### Unverified / suspicious
- STEP 0 (0.1–0.6) all asserted CLOSED with ZERO supporting artifacts in report_18/: no grep output for the removed literals, no audio_profile.json, no ffprobe, no output/whisper_raw.json, no verify_phase2 stdout. Every 0.x closure is UNVERIFIED (failure pattern #1).
- face_tracks_c1.json: spk_a home_region = [2109,825,599,676] but EVERY spk_a box is clustered at x∈[80,220], y∈[1040,1090]. The declared home_region does not contain a single frame of the track it belongs to — this is either a hardcoded/stale ROI or broken tracking. Directly undermines §1.1 'derived home_region, first-frame detection'.
- speaker_timeline agreement_rate = 0.999 is suspiciously perfect (pattern #5). §1.2's PRIMARY evidence is per-mic Silero VAD, which REQUIRES two independent channels — no `ffprobe -show_streams` and no per-channel astats energy provided. qc.json shows audio_channels:1. The entire speaker timeline (and therefore the whole EDL) rests on the still-unproven two-mic vs pre-mixed-stereo assumption.
- cut_on_pause_fraction = 1.0 is not supported by the timeline: the cut at frame 137 sits inside continuous spk_a speech (frames 120–209 all spk_a, no null/silence). At least one of the 3 cuts did not land in a pause, yet AG reports 3/3.
- Reaction shot is 75 frames but preset reaction_shot.duration_frames=45 — deviation unexplained (possible silent min_shot clamp, but not stated).
- both_frames = 0 while frames 199–202 carry overlap:true with speaker spk_a — overlap flagged but never resolved to 'both'.

### Blocking
- PROVE CHANNEL LAYOUT before any speaker-timeline result is accepted: raw `ffprobe -show_streams` of the source AND per-channel `ffmpeg astats` energy showing Ch1 is not dead. Until then §1.2 output is unverified and §1.3 built on top of it cannot advance.
- Resolve home_region [2109,…] vs spk_a boxes at x~150. grep the codebase for 2109 / any fixed ROI and show the derivation. If home_region is hardcoded, that is a Step-0-class literal reappearing inside §1.1.
- Produce the missing Step 0 raw artifacts (grep proving 10.8/35.1/233/frame[900:1500] gone, audio_profile.json, whisper_raw.json, verify_phase2 stdout). Claims of CLOSED without them do not count.
- Reconcile cut_on_pause_fraction=1.0 with the actual timeline at frame 137, or correct the metric.
- PROTOCOL: loop 17 verdict was REVISE with requires_human=true (unresolved). AG advanced through §1.1/§1.2/§1.3 and a full proof render anyway — running past an open human-gate/review boundary.

### Carried (non-blocking)
- speech-vs-room-tone WARN 7.2 dB vs 15 dB target (longstanding audio item).
- framing stability WARN 57px std — crop path not yet quantized; §1.4 BUILD_CROPS territory, but do not let it be silently inherited.
- reaction-shot duration 45 vs realized 75: confirm min_shot clamp is intended behavior and document it.

## Why the loop stopped (Commander must clear)
Two reasons a human must decide. (1) AG ran past an unresolved human-gate: loop 17 closed REVISE + requires_human=true, and AG proceeded to build and render §1.3 across §1.1/§1.2 — a review-boundary breach the Commander should rule on before the loop continues. (2) Accepting this proof is effectively a §1.1–§1.3 section signoff resting on a foundational assumption that has never been proven (two independent mic channels; agreement_rate 0.999 is suspiciously perfect and no ffprobe -show_streams exists), plus a home_region [2109,825,599,676] that does not contain its own track and looks like a reappearing hardcoded literal. Recommended demand to AG once cleared: STOP, revert to the last accepted boundary, and return RAW only — (a) `ffprobe -show_streams <source>` and per-channel `ffmpeg ... astats` energy; (b) `grep -rn 2109` and the home_region derivation code showing it is measured from first-frame detection, not fixed; (c) the Step 0 artifacts (grep for 10.8/35.1/233/frame[900:1500], audio_profile.json, whisper_raw.json, verify_phase2 stdout); (d) the timeline frames around each of the 3 cut boundaries proving cut-on-pause. No tables, no prose summaries — the files themselves.
