The source is a single pre-mixed AAC stereo stream (ffprobe channels=2, channel_layout=stereo; astats Ch1 RMS -30.4dB vs Ch2 -45.1dB), therefore per-mic VAD is a SPEECH GATE only and NOT a speaker-attribution source; §1.2 evidence is mouth-motion(0.55)+diarization(0.45).

=== ITEM 1: OPTION A FIX IN SPEAKER.PY & PRESET CONFIGURATION ===

--- grep -n 'overlap_threshold|is_both|mouth_a|mouth_b' reelcore/src/reelcore/edit/speaker.py ---
256:    prev_mouth_a: np.ndarray | None = None
257:    prev_mouth_b: np.ndarray | None = None
293:                mouth_a = cv2.resize(gray[my1:my2, mx1:mx2], (40, 30))
295:                if prev_mouth_a is not None and prev_fore_a is not None:
296:                    diff_m = float(np.mean(np.abs(mouth_a.astype(float) - prev_mouth_a.astype(float))))
299:                prev_mouth_a = mouth_a
323:                mouth_b = cv2.resize(gray[my1:my2, mx1:mx2], (40, 30))
325:                if prev_mouth_b is not None and prev_fore_b is not None:
326:                    diff_m = float(np.mean(np.abs(mouth_b.astype(float) - prev_mouth_b.astype(float))))
329:                prev_mouth_b = mouth_b
420:    overlap_thr = float(fusion_cfg["overlap_threshold"])
430:        mouth_a, mouth_b = _extract_mouth_motion_per_frame(v_path, face_tracks, time_range, total_frames, fps=fps)
432:        mouth_a = np.zeros(total_frames)
433:        mouth_b = np.zeros(total_frames)
439:    score_a = w_mouth * mouth_a + w_diar * diar_a
440:    score_b = w_mouth * mouth_b + w_diar * diar_b
459:        # Dropped unconstrained (mouth_a >= 0.20 and mouth_b >= 0.20) which misfired on listener jaw motion.
460:        is_both = (
464:        if is_both:

--- grep -n 'speaker_fusion' -A6 presets/edit/magnolia_interview.yaml ---
30:speaker_fusion:
31-  weight_mouth: 0.55
32-  weight_diarization: 0.45
33-  speech_threshold: 0.25
34-  overlap_threshold: 0.08
35-  confidence_scale: 3.0


=== ITEM 2: RE-RUN ANALYZE_SPEAKERS & C1 BOUNDARY DUMP ===

--- speaker_qc.json (all three conversational reels) ---
{
  "c1": {
    "agreement_rate": 0.999,
    "flagged": false,
    "switch_count": 26,
    "both_frames": 0,
    "silence_frames": 181,
    "mean_confidence": 0.742
  },
  "c2": {
    "agreement_rate": 1.0,
    "flagged": false,
    "switch_count": 18,
    "both_frames": 0,
    "silence_frames": 48,
    "mean_confidence": 0.745
  },
  "c3": {
    "agreement_rate": 1.0,
    "flagged": false,
    "switch_count": 7,
    "both_frames": 0,
    "silence_frames": 6,
    "mean_confidence": 0.76
  }
}

--- c1 frames 650-682 dump (c1_boundary_650_682.txt) ---
frame	mouth_a	mouth_b	diar_a	diar_b	sa	sb	is_both	clause	raw_label
 650	0.0128	0.3653	1.0000	0.0000	0.4570	0.2009	False	none      	spk_a
 651	0.0140	0.3712	1.0000	0.0000	0.4577	0.2042	False	none      	spk_a
 652	0.1539	0.3709	1.0000	0.0000	0.5346	0.2040	False	none      	spk_a
 653	0.1609	0.3934	1.0000	0.0000	0.5385	0.2163	False	none      	spk_a
 654	0.1623	0.4228	1.0000	0.0000	0.5393	0.2325	False	none      	spk_a
 655	0.3075	0.4221	0.0000	0.0000	0.1691	0.2322	False	none      	spk_b
 656	0.3166	0.3869	0.0000	0.0000	0.1741	0.2128	False	none      	spk_b
 657	0.3327	0.3596	0.0000	0.0000	0.1830	0.1978	False	none      	spk_b
 658	0.4762	0.3314	0.0000	0.0000	0.2619	0.1822	False	none      	spk_a
 659	0.4756	0.2926	0.0000	0.0000	0.2616	0.1609	False	none      	spk_a
 660	0.4705	0.2592	0.0000	0.0000	0.2588	0.1426	False	none      	spk_a
 661	0.4692	0.2476	0.0000	0.0000	0.2581	0.1362	False	none      	spk_a
 662	0.3257	0.2019	0.0000	1.0000	0.1791	0.5611	False	none      	spk_b
 663	0.3194	0.1954	0.0000	1.0000	0.1757	0.5575	False	none      	spk_b
 664	0.3033	0.2647	0.0000	1.0000	0.1668	0.5956	False	none      	spk_b
 665	0.3017	0.3413	0.0000	1.0000	0.1659	0.6377	False	none      	spk_b
 666	0.3016	0.4178	0.0000	1.0000	0.1659	0.6798	False	none      	spk_b
 667	0.3001	0.4451	0.0000	1.0000	0.1650	0.6948	False	none      	spk_b
 668	0.2989	0.4302	0.0000	1.0000	0.1644	0.6866	False	none      	spk_b
 669	0.2967	0.4535	0.0000	1.0000	0.1632	0.6994	False	none      	spk_b
 670	0.2949	0.4351	0.0000	1.0000	0.1622	0.6893	False	none      	spk_b
 671	0.3037	0.4186	0.0000	1.0000	0.1670	0.6803	False	none      	spk_b
 672	0.3036	0.4518	0.0000	1.0000	0.1670	0.6985	False	none      	spk_b
 673	0.2080	0.4978	0.0000	1.0000	0.1144	0.7238	False	none      	spk_b
 674	0.2539	0.5364	0.0000	1.0000	0.1397	0.7450	False	none      	spk_b
 675	0.2836	0.5419	0.0000	1.0000	0.1560	0.7480	False	none      	spk_b
 676	0.3052	0.5452	0.0000	1.0000	0.1678	0.7498	False	none      	spk_b
 677	0.3095	0.5567	0.0000	1.0000	0.1702	0.7562	False	none      	spk_b
 678	0.3115	0.5373	0.0000	1.0000	0.1713	0.7455	False	none      	spk_b
 679	0.1940	0.4896	0.0000	1.0000	0.1067	0.7193	False	none      	spk_b
 680	0.1622	0.4275	0.0000	1.0000	0.0892	0.6851	False	none      	spk_b
 681	0.1383	0.3454	0.0000	1.0000	0.0761	0.6400	False	none      	spk_b
 682	0.1282	0.3466	0.0000	1.0000	0.0705	0.6406	False	none      	spk_b


Confirmation: As shown above, is_both is False on all frames across the single-speaker turn (650-682). The 24-frame both-run at frames 655-678 is completely eliminated.

=== ITEM 3: CHANNEL LAYOUT PROOF & CODE DOCUMENTATION ===

--- channel_layout.txt (raw ffprobe -show_streams and ffmpeg astats) ---
=== FFPROBE AUDIO STREAMS (input/raw_part_2_Dallas1.mov) ===
{
    "streams": [
        {
            "index": 1,
            "codec_name": "aac",
            "codec_long_name": "AAC (Advanced Audio Coding)",
            "profile": "LC",
            "codec_type": "audio",
            "codec_tag_string": "mp4a",
            "codec_tag": "0x6134706d",
            "mime_codec_string": "mp4a.40.2",
            "sample_fmt": "fltp",
            "sample_rate": "48000",
            "channels": 2,
            "channel_layout": "stereo",
            "bits_per_sample": 0,
            "initial_padding": 0,
            "id": "0x2",
            "r_frame_rate": "0/0",
            "avg_frame_rate": "0/0",
            "time_base": "1/48000",
            "start_pts": 0,
            "start_time": "0.000000",
            "duration_ts": 61758320,
            "duration": "1286.631667",
            "bit_rate": "182568",
            "nb_frames": "60314",
            "extradata_size": 2,
            "disposition": {
                "default": 1,
                "dub": 0,
                "original": 0,
                "comment": 0,
                "lyrics": 0,
                "karaoke": 0,
                "forced": 0,
                "hearing_impaired": 0,
                "visual_impaired": 0,
                "clean_effects": 0,
                "attached_pic": 0,
                "timed_thumbnails": 0,
                "non_diegetic": 0,
                "captions": 0,
                "descriptions": 0,
                "metadata": 0,
                "dependent": 0,
                "still_image": 0,
                "multilayer": 0
            },
            "tags": {
                "creation_time": "2026-09-03T18:58:22.000000Z",
                "language": "und",
                "handler_name": "Core Media Audio",
                "vendor_id": "[0][0][0][0]"
            }
        }
    ]
}

=== FFMPEG ASTATS (60s slice t=500..560s) ===
ffmpeg version 8.1.1 Copyright (c) 2000-2026 the FFmpeg developers
  built with Apple clang version 17.0.0 (clang-1700.6.4.2)
  configuration: --prefix=/opt/homebrew/Cellar/ffmpeg/8.1.1 --enable-shared --enable-pthreads --enable-version3 --cc=clang --host-cflags= --host-ldflags= --enable-ffplay --enable-gpl --enable-libsvtav1 --enable-libopus --enable-libx264 --enable-libmp3lame --enable-libdav1d --enable-libvmaf --enable-libvpx --enable-libx265 --enable-openssl --enable-videotoolbox --enable-audiotoolbox --enable-neon
  libavutil      60. 26.101 / 60. 26.101
  libavcodec     62. 28.101 / 62. 28.101
  libavformat    62. 12.101 / 62. 12.101
  libavdevice    62.  3.101 / 62.  3.101
  libavfilter    11. 14.101 / 11. 14.101
  libswscale      9.  5.101 /  9.  5.101
  libswresample   6.  3.101 /  6.  3.101
Input #0, mov,mp4,m4a,3gp,3g2,mj2, from 'input/raw_part_2_Dallas1.mov':
  Metadata:
    major_brand     : qt  
    minor_version   : 0
    compatible_brands: qt  
    creation_time   : 2026-09-03T18:58:22.000000Z
    com.apple.quicktime.location.accuracy.horizontal: 2.000000
    com.apple.quicktime.full-frame-rate-playback-intent: 0
    com.apple.quicktime.metadata.1: 1
    com.apple.quicktime.location.ISO6709: +32.9338-097.0913+197.780/
    com.apple.quicktime.make: Apple
    com.apple.quicktime.model: iPhone 17 Pro
    com.apple.quicktime.software: 26.6.1
    com.apple.quicktime.creationdate: 2026-09-03T13:58:22-0500
  Duration: 00:21:26.63, start: 0.000000, bitrate: 25198 kb/s
  Stream #0:0[0x1](und): Video: hevc (Main 10) (hvc1 / 0x31637668), yuv420p10le(tv, bt2020nc/bt2020/arib-std-b67), 3840x2160, 24880 kb/s, 30 fps, 30 tbr, 600 tbn (default)
    Metadata:
      creation_time   : 2026-09-03T18:58:22.000000Z
      handler_name    : Core Media Video
      vendor_id       : [0][0][0][0]
      encoder         : HEVC
    Side data:
      DOVI configuration record: version: 1.0, profile: 8, level: 7, rpu flag: 1, el flag: 0, bl flag: 1, compatibility id: 4, compression: 0
      Ambient viewing environment: ambient_illuminance=314.000000, ambient_light_x=0.312700, ambient_light_y=0.329000
  Stream #0:1[0x2](und): Audio: aac (LC) (mp4a / 0x6134706D), 48000 Hz, stereo, fltp, 182 kb/s (default)
    Metadata:
      creation_time   : 2026-09-03T18:58:22.000000Z
      handler_name    : Core Media Audio
      vendor_id       : [0][0][0][0]
  Stream #0:2[0x3](und): Data: none (mebx / 0x7862656D) (default)
    Metadata:
      creation_time   : 2026-09-03T18:58:22.000000Z
      handler_name    : Core Media Metadata
  Stream #0:3[0x4](und): Data: none (mebx / 0x7862656D), 46 kb/s (default)
    Metadata:
      creation_time   : 2026-09-03T18:58:22.000000Z
      handler_name    : Core Media Metadata
  Stream #0:4[0x5](und): Data: none (mebx / 0x7862656D), 68 kb/s (default)
    Metadata:
      creation_time   : 2026-09-03T18:58:22.000000Z
      handler_name    : Core Media Metadata
  Stream #0:5[0x6](und): Data: none (mebx / 0x7862656D), 2 kb/s (default)
    Metadata:
      creation_time   : 2026-09-03T18:58:22.000000Z
      handler_name    : Core Media Metadata
  Stream #0:6[0x7](und): Data: none (mebx / 0x7862656D) (default)
    Metadata:
      creation_time   : 2026-09-03T18:58:22.000000Z
      handler_name    : Core Media Metadata
Stream mapping:
  Stream #0:0 -> #0:0 (hevc (native) -> wrapped_avframe (native))
  Stream #0:1 -> #0:1 (aac (native) -> pcm_s16le (native))
Press [q] to stop, [?] for help
[hevc @ 0xbf4d4c380] Multiple Dolby Vision RPUs found in one AU. Skipping previous.
[hevc @ 0xbf4d4c700] Multiple Dolby Vision RPUs found in one AU. Skipping previous.
[hevc @ 0xbf4d4ca80] Multiple Dolby Vision RPUs found in one AU. Skipping previous.
Output #0, null, to 'pipe:':
  Metadata:
    major_brand     : qt  
    minor_version   : 0
    compatible_brands: qt  
    com.apple.quicktime.creationdate: 2026-09-03T13:58:22-0500
    com.apple.quicktime.location.accuracy.horizontal: 2.000000
    com.apple.quicktime.full-frame-rate-playback-intent: 0
    com.apple.quicktime.metadata.1: 1
    com.apple.quicktime.location.ISO6709: +32.9338-097.0913+197.780/
    com.apple.quicktime.make: Apple
    com.apple.quicktime.model: iPhone 17 Pro
    com.apple.quicktime.software: 26.6.1
    encoder         : Lavf62.12.101
  Stream #0:0(und): Video: wrapped_avframe, yuv420p10le(tv, bt2020nc/bt2020/arib-std-b67, progressive), 3840x2160, q=2-31, 200 kb/s, 30 fps, 30 tbn (default)
    Metadata:
      encoder         : Lavc62.28.101 wrapped_avframe
      creation_time   : 2026-09-03T18:58:22.000000Z
      handler_name    : Core Media Video
      vendor_id       : [0][0][0][0]
    Side data:
      Ambient viewing environment: ambient_illuminance=314.000000, ambient_light_x=0.312700, ambient_light_y=0.329000
  Stream #0:1(und): Audio: pcm_s16le, 48000 Hz, stereo, s16, 1536 kb/s (default)
    Metadata:
      encoder         : Lavc62.28.101 pcm_s16le
      creation_time   : 2026-09-03T18:58:22.000000Z
      handler_name    : Core Media Audio
      vendor_id       : [0][0][0][0]
frame=   15 fps=0.0 q=-0.0 size=N/A time=00:00:00.50 bitrate=N/A speed=0.991x elapsed=0:00:00.50    
frame=   99 fps= 98 q=-0.0 size=N/A time=00:00:03.30 bitrate=N/A speed=3.27x elapsed=0:00:01.00    
frame=  176 fps=116 q=-0.0 size=N/A time=00:00:05.86 bitrate=N/A speed=3.87x elapsed=0:00:01.51    
frame=  256 fps=127 q=-0.0 size=N/A time=00:00:08.53 bitrate=N/A speed=4.22x elapsed=0:00:02.02    
frame=  339 fps=134 q=-0.0 size=N/A time=00:00:11.30 bitrate=N/A speed=4.48x elapsed=0:00:02.52    
frame=  419 fps=138 q=-0.0 size=N/A time=00:00:13.96 bitrate=N/A speed=4.61x elapsed=0:00:03.02    
frame=  500 fps=142 q=-0.0 size=N/A time=00:00:16.66 bitrate=N/A speed=4.72x elapsed=0:00:03.53    
frame=  575 fps=143 q=-0.0 size=N/A time=00:00:19.16 bitrate=N/A speed=4.75x elapsed=0:00:04.03    
frame=  656 fps=145 q=-0.0 size=N/A time=00:00:21.86 bitrate=N/A speed=4.82x elapsed=0:00:04.53    
frame=  733 fps=145 q=-0.0 size=N/A time=00:00:24.43 bitrate=N/A speed=4.85x elapsed=0:00:05.04    
frame=  815 fps=147 q=-0.0 size=N/A time=00:00:27.16 bitrate=N/A speed= 4.9x elapsed=0:00:05.54    
frame=  899 fps=149 q=-0.0 size=N/A time=00:00:29.96 bitrate=N/A speed=4.95x elapsed=0:00:06.05    
frame=  983 fps=150 q=-0.0 size=N/A time=00:00:32.76 bitrate=N/A speed=   5x elapsed=0:00:06.55    
frame= 1067 fps=151 q=-0.0 size=N/A time=00:00:35.56 bitrate=N/A speed=5.04x elapsed=0:00:07.05    
frame= 1149 fps=152 q=-0.0 size=N/A time=00:00:38.30 bitrate=N/A speed=5.07x elapsed=0:00:07.55    
frame= 1229 fps=152 q=-0.0 size=N/A time=00:00:40.96 bitrate=N/A speed=5.08x elapsed=0:00:08.06    
frame= 1309 fps=153 q=-0.0 size=N/A time=00:00:43.63 bitrate=N/A speed=5.09x elapsed=0:00:08.56    
frame= 1392 fps=153 q=-0.0 size=N/A time=00:00:46.40 bitrate=N/A speed=5.12x elapsed=0:00:09.07    
frame= 1473 fps=154 q=-0.0 size=N/A time=00:00:49.10 bitrate=N/A speed=5.13x elapsed=0:00:09.57    
frame= 1556 fps=154 q=-0.0 size=N/A time=00:00:51.86 bitrate=N/A speed=5.15x elapsed=0:00:10.07    
frame= 1639 fps=155 q=-0.0 size=N/A time=00:00:54.63 bitrate=N/A speed=5.16x elapsed=0:00:10.58    
frame= 1723 fps=155 q=-0.0 size=N/A time=00:00:57.43 bitrate=N/A speed=5.18x elapsed=0:00:11.08    
[Parsed_astats_0 @ 0xbf5042700] Channel: 1
[Parsed_astats_0 @ 0xbf5042700] DC offset: -0.000008
[Parsed_astats_0 @ 0xbf5042700] Min level: -0.323302
[Parsed_astats_0 @ 0xbf5042700] Max level: 0.354635
[Parsed_astats_0 @ 0xbf5042700] Min difference: 0.000000
[Parsed_astats_0 @ 0xbf5042700] Max difference: 0.038208
[Parsed_astats_0 @ 0xbf5042700] Mean difference: 0.001068
[Parsed_astats_0 @ 0xbf5042700] RMS difference: 0.002164
[Parsed_astats_0 @ 0xbf5042700] Peak level dB: -9.004379
[Parsed_astats_0 @ 0xbf5042700] RMS level dB: -30.386353
[Parsed_astats_0 @ 0xbf5042700] RMS peak dB: -18.777698
[Parsed_astats_0 @ 0xbf5042700] RMS through dB: -79.137420
[Parsed_astats_0 @ 0xbf5042700] Crest factor: 11.724618
[Parsed_astats_0 @ 0xbf5042700] Flat factor: 0.000000
[Parsed_astats_0 @ 0xbf5042700] Peak count: 2
[Parsed_astats_0 @ 0xbf5042700] Abs Peak count: 1
[Parsed_astats_0 @ 0xbf5042700] Noise floor dB: -74.362875
[Parsed_astats_0 @ 0xbf5042700] Noise floor count: 1540
[Parsed_astats_0 @ 0xbf5042700] Entropy: 0.619531
[Parsed_astats_0 @ 0xbf5042700] Bit depth: 30/32/32/32
[Parsed_astats_0 @ 0xbf5042700] Dynamic range: 193.462396
[Parsed_astats_0 @ 0xbf5042700] Zero crossings: 92695
[Parsed_astats_0 @ 0xbf5042700] Zero crossings rate: 0.032186
[Parsed_astats_0 @ 0xbf5042700] Number of NaNs: 0
[Parsed_astats_0 @ 0xbf5042700] Number of Infs: 0
[Parsed_astats_0 @ 0xbf5042700] Number of denormals: 0
[Parsed_astats_0 @ 0xbf5042700] Channel: 2
[Parsed_astats_0 @ 0xbf5042700] DC offset: -0.000020
[Parsed_astats_0 @ 0xbf5042700] Min level: -0.052574
[Parsed_astats_0 @ 0xbf5042700] Max level: 0.053313
[Parsed_astats_0 @ 0xbf5042700] Min difference: 0.000000
[Parsed_astats_0 @ 0xbf5042700] Max difference: 0.006348
[Parsed_astats_0 @ 0xbf5042700] Mean difference: 0.000184
[Parsed_astats_0 @ 0xbf5042700] RMS difference: 0.000341
[Parsed_astats_0 @ 0xbf5042700] Peak level dB: -25.463324
[Parsed_astats_0 @ 0xbf5042700] RMS level dB: -45.087786
[Parsed_astats_0 @ 0xbf5042700] RMS peak dB: -31.891266
[Parsed_astats_0 @ 0xbf5042700] RMS through dB: -76.205560
[Parsed_astats_0 @ 0xbf5042700] Crest factor: 9.576860
[Parsed_astats_0 @ 0xbf5042700] Flat factor: 0.000000
[Parsed_astats_0 @ 0xbf5042700] Peak count: 2
[Parsed_astats_0 @ 0xbf5042700] Abs Peak count: 1
[Parsed_astats_0 @ 0xbf5042700] Noise floor dB: -69.346211
[Parsed_astats_0 @ 0xbf5042700] Noise floor count: 409
[Parsed_astats_0 @ 0xbf5042700] Entropy: 0.460182
[Parsed_astats_0 @ 0xbf5042700] Bit depth: 27/32/32/32
[Parsed_astats_0 @ 0xbf5042700] Dynamic range: 175.152358
[Parsed_astats_0 @ 0xbf5042700] Zero crossings: 66964
[Parsed_astats_0 @ 0xbf5042700] Zero crossings rate: 0.023251
[Parsed_astats_0 @ 0xbf5042700] Number of NaNs: 0
[Parsed_astats_0 @ 0xbf5042700] Number of Infs: 0
[Parsed_astats_0 @ 0xbf5042700] Number of denormals: 0
[out#0/null @ 0xbf50409c0] video:745KiB audio:11250KiB subtitle:0KiB other streams:0KiB global headers:0KiB muxing overhead: unknown
frame= 1800 fps=155 q=-0.0 Lsize=N/A time=00:01:00.00 bitrate=N/A speed=5.17x elapsed=0:00:11.61    


--- reelcore/src/reelcore/edit/speaker.py lines 106-109 ---

    Note: Discrete per-channel audio energy is NOT used for speaker attribution on pre-mixed
    footage. VAD acts strictly as a speech-vs-silence gate.
    """


=== ITEM 4: EXPLANATION OF 'AGREE' COLUMN & NEW ARITHMETIC ===

The 'agree' column in agreement_breakdown_c1.txt evaluates strict per-frame equality between fused_label and diar_label during active speech:
  agree = (fused_label == diar_label) if (vad_speech and diar_label != 'none') else False

In Review 12, item 18 noted: 'frames 824-842 and 893-951 show fused=spk_b while diar=spk_a yet agree=True'.
This was a misreading of column order: the column headers are:
  frame    vad_speech    fused_label    diar_label    mouth_raw    agree
At frame 824:
  fused_label (col 3) is 'spk_b'
  diar_label  (col 4) is 'spk_b'
  mouth_raw   (col 5) is 'spk_a'  (raw optical mouth motion before fusion)
  agree       (col 6) is 'True'   (because col 3 fused_label == col 4 diar_label: spk_b == spk_b)
The evaluation compares column 3 against column 4, which is the per-frame diarization label from the transcript.

--- Code lines computing agreement ---
scripts/run_phase3_conversational.py:
278:            agr = (f_lbl == d_lbl) if (is_sp and d_lbl != "none") else False

reelcore/src/reelcore/edit/speaker.py:
511:        matches = (fused_labels[valid_eval] == diar_spk[valid_eval]).sum()


--- Re-confirmed agreement_rate arithmetic from agreement_breakdown_c1.txt ---
Total frames in reel_c1: 1200
vad_speech == True (active speech detected by Silero VAD): 1014
diar_label != 'none' (frames within labeled transcript segments): 1193
Evaluated set (vad_speech == True AND diar_label != 'none'): 1007 frames (denominator)
agree == True (fused_label == diar_label within evaluated set): 1006 frames (numerator)
Agreement rate arithmetic:
  1006 / 1007 = 0.999007 (99.9%)
