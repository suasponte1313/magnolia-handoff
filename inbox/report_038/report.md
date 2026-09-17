# Report 038: Dallas Closure Verification + Florida Offset Clarification

Dallas closure verified. The crown_fallback_ratio is in the preset at line 25. The face_framing.py:260 hit is a function parameter default (acceptable fallback), and line 304 now uses the parameter `crown_fallback_ratio` not a hardcoded literal. Other 0.03 hits are in old solver scripts (HR_BAND constants, comments) and unrelated modules (audio/dsp speech_rms, framing/tracks bbox tolerances).

Expected_offset_s discrepancy explanation: The compare files in input/florida/compare/ are already time-sliced to match the video duration (both are 404.71s). The 206.0s from prompt_037 is the offset into the FULL 30-minute TX03_MIC032_20260913_094101_orig.wav where the video begins. The sliced compare file (*_MIC_TX03.wav) was extracted starting at that offset, so within the compare files the expected offset is 0.0s (already aligned). The measured 2.7s offset with weak correlation (-0.027) suggests either: (a) the slicing was slightly off, (b) the correlation is noisy due to different audio characteristics, or (c) need to re-verify the slice boundaries against the original.

## Artifacts

- dallas_preset_grep.txt — crown_fallback_ratio found in preset line 25
- literal_check.txt — 0.03 hits are parameter defaults or unrelated
- source_tiers_tail.txt — final Dallas numbers appended
- florida_ffprobe.txt — video stream metadata
