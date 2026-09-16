=== ITEM 1: CHANNEL LAYOUT PROOF (RAW OUTPUT ONLY) ===

--- 1.a: ffprobe -v error -show_streams -select_streams a input/raw_part_2_Dallas1.mov ---
[STREAM]
index=1
codec_name=aac
codec_long_name=AAC (Advanced Audio Coding)
profile=LC
codec_type=audio
codec_tag_string=mp4a
codec_tag=0x6134706d
mime_codec_string=mp4a.40.2
sample_fmt=fltp
sample_rate=48000
channels=2
channel_layout=stereo
bits_per_sample=0
initial_padding=0
id=0x2
r_frame_rate=0/0
avg_frame_rate=0/0
time_base=1/48000
start_pts=0
start_time=0.000000
duration_ts=61758320
duration=1286.631667
bit_rate=182568
max_bit_rate=N/A
bits_per_raw_sample=N/A
nb_frames=60314
nb_read_frames=N/A
nb_read_packets=N/A
extradata_size=2
DISPOSITION:default=1
DISPOSITION:dub=0
DISPOSITION:original=0
DISPOSITION:comment=0
DISPOSITION:lyrics=0
DISPOSITION:karaoke=0
DISPOSITION:forced=0
DISPOSITION:hearing_impaired=0
DISPOSITION:visual_impaired=0
DISPOSITION:clean_effects=0
DISPOSITION:attached_pic=0
DISPOSITION:timed_thumbnails=0
DISPOSITION:non_diegetic=0
DISPOSITION:captions=0
DISPOSITION:descriptions=0
DISPOSITION:metadata=0
DISPOSITION:dependent=0
DISPOSITION:still_image=0
DISPOSITION:multilayer=0
TAG:creation_time=2026-09-03T18:58:22.000000Z
TAG:language=und
TAG:handler_name=Core Media Audio
TAG:vendor_id=[0][0][0][0]
[/STREAM]

--- 1.b: ffprobe of demuxed WAV actually fed to analysis (output/master_audio_balanced.wav) ---
[STREAM]
index=0
codec_name=pcm_s24le
codec_long_name=PCM signed 24-bit little-endian
profile=unknown
codec_type=audio
codec_tag_string=[1][0][0][0]
codec_tag=0x0001
sample_fmt=s32
sample_rate=48000
channels=1
channel_layout=unknown
bits_per_sample=24
initial_padding=0
id=N/A
r_frame_rate=0/0
avg_frame_rate=0/0
time_base=1/48000
start_pts=N/A
start_time=N/A
duration_ts=61758400
duration=1286.633333
bit_rate=1152000
max_bit_rate=N/A
bits_per_raw_sample=24
nb_frames=N/A
nb_read_frames=N/A
nb_read_packets=N/A
DISPOSITION:default=0
DISPOSITION:dub=0
DISPOSITION:original=0
DISPOSITION:comment=0
DISPOSITION:lyrics=0
DISPOSITION:karaoke=0
DISPOSITION:forced=0
DISPOSITION:hearing_impaired=0
DISPOSITION:visual_impaired=0
DISPOSITION:clean_effects=0
DISPOSITION:attached_pic=0
DISPOSITION:timed_thumbnails=0
DISPOSITION:non_diegetic=0
DISPOSITION:captions=0
DISPOSITION:descriptions=0
DISPOSITION:metadata=0
DISPOSITION:dependent=0
DISPOSITION:still_image=0
DISPOSITION:multilayer=0
[/STREAM]

--- 1.b (reference): ffprobe of raw demuxed stereo WAV (output/raw_demux_stereo.wav) ---
[STREAM]
index=0
codec_name=pcm_s24le
codec_long_name=PCM signed 24-bit little-endian
profile=unknown
codec_type=audio
codec_tag_string=[1][0][0][0]
codec_tag=0x0001
sample_fmt=s32
sample_rate=48000
channels=2
channel_layout=stereo
bits_per_sample=24
initial_padding=0
id=N/A
r_frame_rate=0/0
avg_frame_rate=0/0
time_base=1/48000
start_pts=N/A
start_time=N/A
duration_ts=61758400
duration=1286.633333
bit_rate=2304000
max_bit_rate=N/A
bits_per_raw_sample=24
nb_frames=N/A
nb_read_frames=N/A
nb_read_packets=N/A
DISPOSITION:default=0
DISPOSITION:dub=0
DISPOSITION:original=0
DISPOSITION:comment=0
DISPOSITION:lyrics=0
DISPOSITION:karaoke=0
DISPOSITION:forced=0
DISPOSITION:hearing_impaired=0
DISPOSITION:visual_impaired=0
DISPOSITION:clean_effects=0
DISPOSITION:attached_pic=0
DISPOSITION:timed_thumbnails=0
DISPOSITION:non_diegetic=0
DISPOSITION:captions=0
DISPOSITION:descriptions=0
DISPOSITION:metadata=0
DISPOSITION:dependent=0
DISPOSITION:still_image=0
DISPOSITION:multilayer=0
[/STREAM]

--- 1.c: ffmpeg astats per channel on demuxed WAV actually fed to analysis (output/master_audio_balanced.wav) ---
[Parsed_astats_0 @ 0x997008fc0] Channel: 1
[Parsed_astats_0 @ 0x997008fc0] Peak level dB: -1.000001
[Parsed_astats_0 @ 0x997008fc0] RMS level dB: -26.115172
[Parsed_astats_0 @ 0x997008fc0] Peak level dB: -1.000001
[Parsed_astats_0 @ 0x997008fc0] RMS level dB: -26.115172

--- 1.c (reference): ffmpeg astats per channel on raw demuxed stereo WAV (output/raw_demux_stereo.wav) ---
[Parsed_astats_0 @ 0xc08c40900] Channel: 1
[Parsed_astats_0 @ 0xc08c40900] Peak level dB: -0.861720
[Parsed_astats_0 @ 0xc08c40900] RMS level dB: -33.417061
[Parsed_astats_0 @ 0xc08c40900] Channel: 2
[Parsed_astats_0 @ 0xc08c40900] Peak level dB: -7.954176
[Parsed_astats_0 @ 0xc08c40900] RMS level dB: -32.819906
[Parsed_astats_0 @ 0xc08c40900] Peak level dB: -0.861720
[Parsed_astats_0 @ 0xc08c40900] RMS level dB: -33.108228

--- 1.d: ONE-LINE CHANNEL REALITY STATEMENT ---
The master is ONE pre-mixed stereo stream (recorded on iPhone 17 Pro, demuxed to mono for analysis), NOT two independent microphone channels; per-mic VAD as primary does not exist, and fusion is re-specified as diarization-primary with mouth-motion tiebreak only.

=== ITEM 2: LRA RECONCILIATION ===

--- PROPOSED BOUND & ONE-LINE REASON ---
Proposed bound: lra_max_lu: 11.0 in presets/delivery/social.yaml.
Reason: An LRA bound of <= 11.0 LU preserves natural dialogue dynamics between speakers on vertical mobile video while ensuring quiet passages do not fall below phone speaker noise floors, matching EBU R128 shortform dialogue practice.

--- PRESET LINE (grep -n -C 2 "lra_max_lu" presets/delivery/social.yaml) ---
6-  loudness_tolerance_lufs: 1.0
7-  true_peak_max_dbtp: -1.0
8:  lra_max_lu: 11.0
9-  sample_rate_hz: 48000
10-  channels: 2

--- QC VALUE FROM FRESH reel_1.qc.json (grep -n -C 4 "loudness range" output/reel_1.qc.json) ---
37-      "expected": "<= -1.0 dBTP",
38-      "detail": ""
39-    },
40-    {
41:      "name": "loudness range",
42-      "status": "PASS",
43-      "measured": "8.4 LU",
44-      "expected": "<= 11.0 LU",
45-      "detail": ""

=== CARRIED FORWARD ITEMS ===

1. Room resonance 233 Hz WARN persists while notch targets [486.3, 474.6, 416.0]:
Preflight run_preflight analyzes raw slice input FFT, selecting top-3 spectral peaks in the search band (486.3 Hz at +11.2 dB, 474.6 Hz at +7.8 dB, 416.0 Hz at +7.4 dB). The 233 Hz (+14.2 dB) peak is measured post-render by QC on the compressed mixdown where speech harmonics concentrate; preflight search/ranking will be tuned in Phase 4 to capture 233 Hz before render.

2. Left/right balance WARN (90/10) and framing stability WARN (30px std vs <25px):
Both remain open for Phase 3 §1.4 (BUILD_CROPS) to resolve via subject re-centering and path quantization holds/eased moves.
