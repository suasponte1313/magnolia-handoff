# Report 039: Verification Artifacts

face_framing.py lines 300-310 confirm the variable `crown_fallback_ratio` is used (not literal 0.03). Line 304: `crown_y = face_top - crown_fallback_ratio * face_height`.

Florida video status: Video was offloaded 2026-09-17, now at input/florida/video/Video-Sep-13-2026-9-44-27-AM.mov (3840x2160 HEVC, 30fps, 404.71s). Updated SOURCE_TIERS.md to reflect INGESTED status.

Audio sync: Re-sliced mic at 206.0s offset, tested correlation across offsets 0-300s in 10s steps - all show weak correlation (-0.02 to -0.03). Root cause: TX03 mic is CLIPPED (prompt_037: peak 0.0 dB, flat factor 23) and camera vs mic have incompatible data characteristics (int16 vs float32, vastly different RMS levels). Cross-correlation cannot verify sync with confidence. Alternative sync needed: visual cues or diarization-based speaker identification.

## Artifacts

- face_framing_304.txt — lines 300-310 showing variable usage
- mic_reslice_206s.wav — fresh slice from original at 206.0s
- audio_analysis.txt — peak/RMS comparison
- sync_analysis.txt — correlation failure analysis
