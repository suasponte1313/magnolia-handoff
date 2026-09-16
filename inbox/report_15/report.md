=== ITEM 1: CHANNEL REALITY & VAD-VS-DIARIZATION SPEECH AGREEMENT ===

We cannot derive a per-speaker VAD signal because there is only one usable audio channel on this pre-mixed footage (Ch2 is dead/ambient at -45.1 dB); Silero VAD yields speech-vs-silence only, not per-speaker attribution.

--- Script lines running Silero VAD on downmixed mono audio (reelcore/src/reelcore/edit/speaker.py:117-136) ---
    cmd_vad = [
        "ffmpeg", "-v", "error",
        "-ss", str(t_start), "-t", str(duration),
        "-i", target_audio,
        "-ac", "1", "-ar", str(sample_rate),
        "-f", "f32le", "-",
    ]
    try:
        raw_vad = subprocess.run(cmd_vad, capture_output=True, check=True).stdout
        a_vad = np.frombuffer(raw_vad, dtype=np.float32)
        if a_vad.size > 0:
            from silero_vad import load_silero_vad, get_speech_timestamps
            import torch
            model = load_silero_vad()
            ts = get_speech_timestamps(torch.from_numpy(a_vad.copy()), model, sampling_rate=sample_rate, return_seconds=True)
            speech_mask = np.zeros(total_frames, dtype=bool)
            for seg in ts:
                f0 = max(0, int(round(seg["start"] * fps)))
                f1 = min(total_frames, int(round(seg["end"] * fps)))
                speech_mask[f0:f1] = True


--- VAD speech activity vs Diarization speech activity on c1 (vad_diar_speech_agreement_c1.txt tail) ---
1180	True      	True       	True
1181	True      	True       	True
1182	True      	True       	True
1183	True      	True       	True
1184	True      	True       	True
1185	True      	True       	True
1186	True      	True       	True
1187	True      	True       	True
1188	False     	True       	False
1189	False     	True       	False
1190	False     	True       	False
1191	False     	True       	False
1192	False     	True       	False
1193	False     	True       	False
1194	False     	True       	False
1195	False     	True       	False
1196	False     	True       	False
1197	False     	True       	False
1198	False     	True       	False
1199	False     	True       	False

--- SUMMARY ---
numerator=1007
denominator=1200
ratio=0.839167


Note: Full 1200-frame per-frame file provided in raw artifact vad_diar_speech_agreement_c1.txt.
Numerator (frames where VAD speech detection matches Diarization speech activity): 1007
Denominator (total frames): 1200
Ratio: 0.839167 (83.9%)

=== ITEM 2: CHANNEL REALITY & CODE PATH AUDIT ===

Confirmation: The source video input/raw_part_2_Dallas1.mov contains a single pre-mixed AAC stereo stream. Channel 1 contains the mixed conversation (RMS -30.39 dB), while Channel 2 is ambient bleed 14.7 dB down (RMS -45.09 dB). Zero code paths assume discrete Ch0/Ch1 mic attribution; per-mic VAD attribution does not exist in the codebase.

--- ffprobe stream snippet (channel_layout.txt) ---
[Audio Stream Index 1]
codec_name: aac
codec_long_name: AAC (Advanced Audio Coding)
channels: 2
channel_layout: stereo
sample_rate: 48000

--- ffmpeg astats Ch1 vs Ch2 RMS snippet (channel_layout.txt) ---
[Parsed_astats_0 @ 0xbf5042700] Channel: 1
[Parsed_astats_0 @ 0xbf5042700] RMS level dB: -30.386353
[Parsed_astats_0 @ 0xbf5042700] Channel: 2
[Parsed_astats_0 @ 0xbf5042700] RMS level dB: -45.087786

--- grep -rn 'diar_a|diar_b|channel' reelcore/src/reelcore/edit/speaker.py ---
reelcore/src/reelcore/edit/speaker.py:6:3. VAD: Speech-vs-silence gate only (no per-channel audio energy attribution).
reelcore/src/reelcore/edit/speaker.py:107:    Note: Discrete per-channel audio energy is NOT used for speaker attribution on pre-mixed
reelcore/src/reelcore/edit/speaker.py:164:    """Compatibility shim returning speech mask replicated across channels."""
reelcore/src/reelcore/edit/speaker.py:179:    diar_a = np.zeros(total_frames, dtype=float)
reelcore/src/reelcore/edit/speaker.py:180:    diar_b = np.zeros(total_frames, dtype=float)
reelcore/src/reelcore/edit/speaker.py:228:            diar_a[f_start:f_end] = 1.0
reelcore/src/reelcore/edit/speaker.py:230:            diar_b[f_start:f_end] = 1.0
reelcore/src/reelcore/edit/speaker.py:232:    return diar_a, diar_b
reelcore/src/reelcore/edit/speaker.py:423:    # 1. VAD: Speech-vs-silence gate (no per-channel audio energy attribution)
reelcore/src/reelcore/edit/speaker.py:436:    diar_a, diar_b = _extract_diarization_per_frame(transcript_path, time_range, total_frames, fps=fps)
reelcore/src/reelcore/edit/speaker.py:439:    score_a = w_mouth * mouth_a + w_diar * diar_a
reelcore/src/reelcore/edit/speaker.py:440:    score_b = w_mouth * mouth_b + w_diar * diar_b
reelcore/src/reelcore/edit/speaker.py:461:            (diar_a[f] > 0.5 and diar_b[f] > 0.5)
reelcore/src/reelcore/edit/speaker.py:507:    valid_eval = speech_mask & ((diar_a > 0.5) ^ (diar_b > 0.5))
reelcore/src/reelcore/edit/speaker.py:510:        diar_spk = np.where(diar_a > diar_b, "spk_a", "spk_b")


=== ITEM 3: EXERCISING 'BOTH' / OVERLAP BRANCH ON SYNTHETIC FRAME SET ===

--- Synthetic test exercising is_both branch (output/PHASE_3_CONVERSATIONAL/overlap_branch_test.txt) ---
# Synthetic frame set exercising is_both / overlap branch in reelcore/src/reelcore/edit/speaker.py
frame	desc	sa	sb	is_both	raw_decision	confidence	overlap
    0	Both moving mouths equally during speech (score-AND)   	0.275	0.275	True   	both        	0.500	True
    1	Both moving mouths slightly unequal (|sa-sb| < 0.08)   	0.303	0.264	True   	both        	0.500	True
    2	Transcript marks simultaneous crosstalk (diar-AND)     	0.505	0.505	True   	both        	0.505	True
    3	Both mouth + both diar strong overlap                  	0.780	0.780	True   	both        	0.780	True
    4	Unequal mouth (|sa-sb| = 0.11 >= 0.08) -> single speaker	0.330	0.220	False  	spk_a       	0.582	False
    5	Below speech_threshold (sa < 0.25) -> single speaker   	0.165	0.165	False  	spk_a       	0.500	False


=== STEP 0 ITEMS 0.1 - 0.6 STATUS RESTATEMENT ===

0.1: Remove REEL_CONFIGS per-clip audio constants (reelcore/src/reelcore/audio/dsp.py & presets/audio/magnolia_interview.yaml) — STATUS: CLOSED (derived from audio_profile.json + preset bounds).
0.2: Delete literals headroom_pct=10.8 and centre_x_pct=35.1 (scripts/render_preset_reel.py:506-507) — STATUS: CLOSED (read dynamically from qc.json).
0.3: Measure Centre X with patched QC connectedComponentsWithStats (reelcore/src/reelcore/qc.py:248) — STATUS: CLOSED (measured at 33.1% in output/reel_1.qc.json).
0.4: Revert transcript edit, re-transcribe with vocabulary (presets/captions/vocabulary.txt & output/dallas1_transcript.json) — STATUS: CLOSED (Whisper re-transcription with initial_prompt).
0.5: Make verify_phase2.py Check 1 real fresh render with QC diff (scripts/verify_phase2.py:33-80) — STATUS: CLOSED (fresh isolated render to output/test_verify_manifest with tolerance diffs).
0.6: LRA 8.2 proposing bound, ROI first-frame derivation, 233 literal removed in scripts/ (reelcore/src/reelcore/framing/tracks.py & scripts/) — STATUS: CLOSED (zero occurrences of 233 in scripts/, ROI derived per clip).
