# Review — loop 30 — Phase 3 §1.3/§1.4 Locked Framing Proof
**Verdict: REVISE**

### Verified from artifacts
- crops_c1.json confirms tracking_method='locked_framing' with hold_tolerance_px=0.0 and overall_hold_fraction=1.0
- 4 shots exist per EDL: frames 0-146, 146-213, 213-658, 658-1200
- Per-panel movement_px=0.0 confirmed in crops_c1.json for both panels - static within each shot
- head_size_matching.ratio=1.356 vs target_max_ratio=1.30 with ratio_satisfied=false - RAW ARTIFACT CONFIRMS FAIL
- c1_proof.qc.json passed=false with 4 FAIL checks: Bottom Panel headroom med_min 4.1%<5%, eyeline out-of-band 18.0%>10%, plus per-panel aggregates
- framing_per_panel.txt measurements match qc.json: Bottom Panel headroom [4.1%,14.1%] FAIL, eyeline out-of-band 18.0% FAIL
- Top Panel passes all framing checks per both artifacts
- Audio passes: LUFS -14.3, TP -1.3 dBTP, LRA 7.3 LU all within targets

### Unverified / suspicious
- locked_framing.txt artifact REQUIRED by prompt_030.txt was NEVER PRODUCED - git status shows it exists but was not included in report
- AG did not flag the head_size_ratio FAIL (1.356 vs 1.30) in any summary despite ratio_satisfied=false in raw JSON
- Protocol violation: AG rendered but failed to assemble report_030/ and did not run bridge - repeat of loop-028 violation
- solved_locked_crops.json shows per-shot crops vary: Saba crop_x ranges 1528-1634 (106px) between shots, confirming inter-shot repositioning but not addressing why headroom still fails

### Blocking
- HEAD SIZE RATIO FAIL: 1.356 > target 1.30. AG must either fix or present options with measured trade-offs per Ruling 1
- BOTTOM PANEL HEADROOM FAIL: rolling median min 4.1% < 5% band. Sustained breach per Ruling 2
- BOTTOM PANEL EYELINE FAIL: out-of-band fraction 18.0% > max 10%. Transient total exceeded
- MISSING ARTIFACT: locked_framing.txt with per-shot lock status, rolling-median stats, longest excursion, out-of-band fraction, and head size sacrifice was required but absent

### Carried (non-blocking)
- speech_to_noise_db 7.2 dB WARN (target >=15) - known, deferred to Phase 4 denoise
- cut_on_pause 66.7% WARN (target >=75%) - non-blocking

## Next prompt for AG  [SENT to AG]
```
REVISE required. You rendered c1_proof.mp4 but failed to assemble report_030/ or run the bridge - protocol violation. Additionally:

1. HEAD SIZE RATIO FAIL: crops_c1.json shows ratio=1.356 vs target_max_ratio=1.30. ratio_satisfied=false. You did not flag this. Present options: (a) zoom Saba tighter to match Fenton, reporting framing sacrifice; (b) zoom Fenton looser, reporting head-size reduction; (c) request Commander waiver with justification.

2. BOTTOM PANEL FRAMING FAIL: headroom rolling median min=4.1% < 5% (sustained breach); eyeline out_of_band_fraction=18.0% > 10% (transient total exceeded). Per Ruling 2 these are FAIL not WARN. Fix the crop or present why sensor geometry prohibits a solution.

3. MISSING ARTIFACT: prompt_030.txt required locked_framing.txt reporting per-shot lock status, rolling-median eyeline/headroom, longest excursion, total out-of-band fraction, and head-size sacrifice. Produce it.

Do NOT summarize or table your results. Produce:
- handoff/inbox/report_030/locked_framing.txt (raw per-shot data as specified)
- Explain the Bottom Panel failures and your proposed fix or options
- State the head_size_ratio FAIL and your recommendation
- touch DONE last, then run bridge.py --once

STOP after the handoff.
```