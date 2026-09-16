# Report 020 — Phase 3 §1.2 & §1.3 Conversational Multi-Cam Verification (c1)

## Executive Summary
All corrections requested in prompt_020 have been executed and verified against raw data:
1. Real flag logic in `speaker.py`: `flagged = bool(agreement_rate < 0.8)`. Recomputed for c1: `agreement_rate = 0.4967` -> `flagged = True`.
2. Sole speaker authority: Diarization is set as sole authority (`weight_diarization: 1.0`, `weight_mouth: 0.0`) in `presets/edit/magnolia_interview.yaml`. Mouth optical flow is neutralized from cut decisions and documented as a known limitation deferred to future calibration.
3. Uncalibrated confidence floor removed: Replaced artificial `0.700` baseline with measured speech token detection probabilities from Whisper (`_extract_word_probabilities_per_frame`). Confidence on c1 now exhibits 220 distinct measured values (mean 0.839, min 0.150, max 0.980).
4. Re-emitted proof: Full virtual multi-cam proof re-rendered for c1 (945.0s–985.0s) with 4 shots, 3 cuts, 3/3 cut-on-pause, and automated QC verification.

---

## 1. Flag Logic Correction (Item 1)
- Source file: `reelcore/src/reelcore/edit/speaker.py` line ~618.
- Previous line: `flagged = False` (hardcoded).
- Corrected code:
```python
    valid_eval = speech_mask & ((diar_a > 0.5) ^ (diar_b > 0.5)) & ((mouth_a > 0.05) | (mouth_b > 0.05))
    if valid_eval.any():
        mouth_spk = np.where(mouth_a > mouth_b, "spk_a", "spk_b")
        diar_spk = np.where(diar_a > diar_b, "spk_a", "spk_b")
        matches = (mouth_spk[valid_eval] == diar_spk[valid_eval]).sum()
        agreement_rate = float(matches / valid_eval.sum())
    else:
        agreement_rate = 0.50

    flagged = bool(agreement_rate < 0.8)
```
- Recomputed on c1:
  - `agreement_rate`: 0.4967
  - `flagged`: True
- Artifact: `flag_fix.txt`

---

## 2. Diarization Authority & Weight Neutralization (Item 2)
- Preset modified: `presets/edit/magnolia_interview.yaml`
```yaml
speaker_fusion:
  weight_mouth: 0.0
  weight_diarization: 1.0
  speech_threshold: 0.25
  overlap_threshold: 0.08
  confidence_scale: 3.0
```
- Code modified: `speaker.py` checks `w_mouth > 0.0` before attempting boundary shifts; with `weight_mouth: 0.0`, diarization is the sole speaker authority.
- Limitation note: Mouth-motion optical flow on this camera setup yields ~49.7% agreement with transcript diarization (chance level). It is safely deactivated (`weight_mouth: 0.0`) so it cannot corrupt cut timing.
- Artifact: `weights.txt`

---

## 3. Measured Detection Confidence (Item 3)
- Root cause: The prior formula `m_conf = 0.70 + 0.25 * clip(mouth_diff)` collapsed to flat 0.700 whenever mouth difference was <= 0 (454 frames in c1).
- Correction: Replaced with frame-by-frame measured token detection confidence extracted from Whisper posterior word probabilities (`_extract_word_probabilities_per_frame`).
- C1 measured stats:
  - Minimum confidence: 0.150
  - Maximum confidence: 0.980
  - Mean confidence: 0.839
  - Distinct values: 220
  - Occurrences of 0.700: 1 (incidental rounding)
- Artifact: `confidence_fix.txt`

---

## 4. Multi-Cam Proof Render & EDL Metrics (c1)
- Rendered video: `output/PHASE_3_CONVERSATIONAL/c1_proof.mp4` (1080x1920 @ 30fps, 40.0s).
- Artifact: `edl_c1.json`
  - Shot 1: frames [0 - 137] (4.57s) | CAM_A (spk_a) | intro
  - Shot 2: frames [137 - 212] (2.50s) | CAM_B (spk_b) | reaction
  - Shot 3: frames [212 - 662] (15.00s) | CAM_A (spk_a) | speaker_change
  - Shot 4: frames [662 - 1200] (17.93s) | CAM_B (spk_b) | speaker_change
- Artifact: `shots_metrics_c1.txt`
  - Total frames: 1200 (137 + 75 + 450 + 538)
  - Shot count: 4 | Switches: 3 | Switches/min: 4.5
  - Mean shot duration: 10.0s | Min shot duration: 2.50s (75 frames)
  - Cut on pause hits: 3 / 3 (1.000)
- Artifact: `c1_proof.qc.json`
  - Status: PASSED (14 checks, 0 failed, 2 warnings)
  - WARN: Speech vs room tone SNR = 7.2 dB (target >= 15.0 dB)
  - WARN: Framing stability = 58px std (target < 25px, scheduled for §1.4 crop quantizer)
- Artifact: `render_stdout_c1.txt`

---

## 5. Carried Verifications & Evidence Files
- Distinct Check 1 QC files:
  - `reference_reel_1.qc.json` (generated 2026-09-09 18:41)
  - `fresh_reel_1.qc.json` (generated 2026-09-10 00:46)
- Foundation raw artifacts:
  - `ffprobe_streams.txt` & `channel_energy.txt`: single stereo channel proof.
  - `roi_grep.txt` & `home_region_code.txt`: dynamic first-frame ROI derivation proof (0 grep matches).
  - `step0_grep.txt`: 0 grep matches for removed Step 0 literals.
  - `audio_profile.json` & `verify_stdout.txt`: complete Step 0 / Phase 2 verification stdout.
  - `cut_boundaries_c1.txt`: ±5 frames around all 3 cut boundaries proving cut-on-pause.
