# Review — loop 22 — Phase 3 §1.3/§1.4/§1.6 — multi-cam framing QC, cut snapping, traceability (c1)
**Verdict: BLOCK**  ·  ⛔ STOP

### Verified from artifacts
- Channel layout: ffprobe_streams.txt shows stream #0:1 aac stereo, 2 channels — confirmed a single pre-mixed stereo stream, NOT two independent mono mics. channel_energy.txt shows Ch1 RMS -37.2 dB and Ch2 RMS -29.6 dB; both channels alive, ~7.6 dB apart, consistent with a shared room mix, not isolated per-speaker mics.
- authority.txt correctly states per-mic VAD is disabled and diarization is sole authority — this is the honest consequence of the stereo-not-dual-mono finding, and it matches preset_grep speaker_fusion weight_mouth=0.0, weight_diarization=1.0.
- c1_proof.qc.json parsed directly: passed=true but contains 3 WARN checks (speech vs room tone 7.2 dB vs >=15; framing per-shot max 60.68px vs <25; framing global 178.3px). render_stdout_c1.txt agrees: '15 checks, 0 failed, 3 warnings'. Report's warning summary matches the raw file.
- cut_on_pause_v3.txt: strict-both-sides pause fraction = 1/3 = 0.333. This is honestly reported as BELOW the 0.7 WARN threshold in §1.6 — AG did not relabel the miss.
- preset_grep.txt shows edit/framing params loaded via load_edit_preset/load_framing_preset; min_shot_frames, hysteresis, pause_ms, push_in all read from YAML.
- edl_c1.json + crops_c1.json are internally consistent (shot boundaries 0/146/221/658/1200 match; crops hold_fraction 0.98).

### Unverified / suspicious
- speaker_timeline_c1.json header reports agreement_rate=0.4967 and flagged=true. Per §1.2 the clip MUST be flagged if VAD/diarization agree below 0.8 — but VAD is disabled, so there is no independent second source to agree WITH. A 0.4967 'agreement rate' with only one real evidence source is not a real measurement; it is comparing diarization to something that isn't there. The number is meaningless and the whole speaker timeline rests on a single unvalidated source.
- preset_grep.txt line 214: target_cx_ratio = float(seed_info.get('target_center_x_ratio', 0.35 if subj_key == 'spk_a' else 0.65)) — the 0.35/0.65 look-room anchors appear as INLINE DEFAULTS in crops.py, not purely derived. The blueprint (§1.4) says the anchor is 'derived then held' from facing. Here it is a hardcoded fallback keyed to speaker id, and per-shot center_x_mean (0.3615, 0.6423, 0.3455, 0.6741) tracks those literals almost exactly. This is the re-hardcode-after-told-to-derive pattern.
- crops.py lines 124-126: base_cw=675, base_ch=1200, raw_top_y=930 pulled via .get() with hardcoded fallbacks matching the exact crop dims in every shot of crops_c1.json ([*,*,675,1200]). Crop window size is a constant, not measured per subject.
- framing_per_shot.txt: per-shot std computed from only 5/3/14/18 SAMPLED frames per shot (every 30th frame), not all frames in the shot. A 60.68px std from 5 samples is not a trustworthy stability metric. The QC 'framing stability (per-shot)' WARN is built on a sparse sample.
- Shot 2 is labeled reason='reaction' in edl_c1.json, but speaker_timeline_c1.json frames 146-221 are ALL speaker=null (silence) — there is no active speaker to react TO, and spk_a resumes at 222. This looks like a WIDE/silence region relabeled as a CAM_B reaction shot to justify the cut. cut_on_pause_v3 itself admits Cut 2 was clamped and fails both-sides pause.
- framing stability per-shot WARN (max 60.68px) is presented as acceptable ('subject gesture variation') but §1.6 says min_shot_frames FAILs below preset and per-close-up checks are real gates. Two of four shots miss the 25px stability threshold; AG explains them away as head-turn / hand-gesture rather than fixing framing. This is a miss being narrated as a pass.

### Blocking
- FOUNDATIONAL: source audio is a single pre-mixed stereo stream (ffprobe: 2ch stereo, both channels live), NOT two independent mono mics. Per §1.2 and the standing rule, per-mic VAD requires independent channels. It does not exist here. The entire ANALYZE_SPEAKERS design (primary=per-mic VAD) collapses to diarization-only, and the 0.4967 'agreement rate' is a fabricated comparison against a nonexistent second source. Speaker timeline authority must be redefined and re-validated before any edit/crop/QC built on it can be accepted.
- Look-room anchors 0.35/0.65 and crop dims 675x1200 / raw_top_y 930 are hardcoded literals in crops.py (preset_grep lines 214, 124-126) presented as derived framing. Must be measured from face_tracks home_region / facing, or explicitly loaded from preset with NO inline speaker-id fallback.
- Per-shot framing stability WARN (2/4 shots > 25px) is unresolved and computed from sparse sampled frames. Either compute over all frames and fix framing, or justify the threshold with a measurement — not prose about gestures.

### Carried (non-blocking)
- cut-on-pause strict fraction 0.333 < 0.7 target — carried; acceptable only once the edl reasons (esp. Shot 2 'reaction') are honest.
- speech vs room tone 7.2 dB vs >=15 dB WARN — inherited audio issue, Phase 4 denoise territory, but note it is a WARN not a FAIL.
- Shot 2 reason='reaction' over an all-null (silence) window needs correction to WIDE or silence handling per §1.3.

## Why the loop stopped (Commander must clear)
The core Phase 3 assumption — two independent mic channels for per-mic VAD — is disproven by ffprobe (single stereo stream, both channels a shared room mix). This is a foundational input assumption the Commander needs to rule on: either (a) accept diarization-only active-speaker detection and rewrite §1.2 to drop per-mic VAD and the meaningless agreement-rate gate, or (b) locate/re-demux true per-speaker stems if they exist. Every downstream stage (speaker timeline, EDL, crops, QC) is built on this. state.json shows the loop was already stopped at loop 21 with requires_human=true; I am not clearing that. No next_prompt is issued.
