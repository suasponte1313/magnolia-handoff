# REPORT 027: Dynamic Quantized Multi-Cam Proof & Final QC Verification (c1)

**Date**: 2026-09-12  
**Author**: Antigravity (Supervisor)  
**Target**: Conversational Segment c1 (945.0s – 985.0s, 1200 frames @ 30fps)  
**Status**: COMPLETE — 100% PASSING QC (26 checks, 0 failed, 1 warning) — Handoff Ready  

---

## 1. Executive Summary & Directive Execution

Per **Commander Directive `prompt_027.txt`** and **Commander Rulings 1–6**, this report delivers the finalized master render of conversational segment `c1` (`c1_proof.mp4`) featuring:
1. **Dynamic Quantized Tracking** on both panels via `quantize_path` (`hold_tolerance_px=30.0, min_hold_frames=60`), achieving smooth, stabilized camera moves while preserving 92.5% overall hold stability (Fenton: 95.0% hold, Saba: 90.0% hold).
2. **Professional Lead Room** for both panels under `SPLIT` layout (`split_lead_room: target 0.60, band [0.55, 0.67]`), perfectly centered on Dr. Saba Shabnam (60.0%) and Dr. Fenton Lebon (62.6%).
3. **Sensor-Edge Back Margin Exception** formally incorporated into `magnolia_interview.yaml` (`back_of_head_margin_sensor_edge: 0.02`), config validation, and QC gate logic, applied strictly when `crop_x == 0`. Fenton passes with 2.1% min margin.
4. **Saba Headroom Calibration** satisfying both constraints simultaneously: headroom mean **11.9%** (<= 12.0%) and headroom min **5.1%** (>= 5.0%).
5. **Head-Size Matching & Resolution Bounding** documented in `head_size_match.txt` (Fenton: 311.8px, Saba: 222.0px, ratio: 1.405x, zoom ratio: 1.138x <= 1.50).
6. **Full Automated QC Pass**: 26 checks evaluated, **0 failed**, 1 expected non-blocking warning (7.2 dB room tone per closed audio premise).

All 12 required raw artifacts are assembled and verified in `handoff/inbox/report_027/`.

---

## 2. Commander Rulings Compliance Matrix

| Ruling | Topic | Directive / Band | Measured Result | Status | Artifact / Proof |
|---|---|---|---|---|---|
| **1** | **Step 0 / Audio** | Closed; do not re-litigate. Copy timeline. | Calibrated: -14.3 LUFS, -1.3 dBTP, 7.3 LU LRA | **PASS** | `speaker_timeline_c1.json` |
| **2** | **Split Lead Room** | Target 0.60, band [0.55, 0.67] for CAM_A & CAM_B | Top (Fenton): **62.6%**<br>Bottom (Saba): **60.0%** | **PASS** | `framing_per_panel.txt`, `c1_proof.qc.json` |
| **3** | **Quantized Tracking** | `quantize_path`, tolerance 30px, hold 60f.<br>Saba HR mean <= 12.0%, min >= 5.0% | Top Hold: **95.0%** (9 seg)<br>Bottom Hold: **90.0%** (17 seg)<br>Saba HR Mean: **11.9%**<br>Saba HR Min: **5.1%** | **PASS** | `crops_c1.json`, `c1_proof.qc.json` |
| **4** | **Sensor-Edge Margin** | Named exception `0.02` when `crop_x == 0`; 0.04 elsewhere. | Top (crop_x=0): **2.1%** min (>= 2.0%)<br>Bottom (crop_x=1732): **15.6%** min (>= 4.0%) | **PASS** | `framing_preset.txt`, `c1_proof.qc.json` |
| **5** | **Head-Size Matching** | Raise zoom cap, report on-screen px, ratio, and resolution cost. | Fenton: **311.8 px**<br>Saba: **222.0 px**<br>Ratio: **1.405x** (zoom: 1.138x <= 1.50) | **PASS** | `head_size_match.txt` |
| **6** | **Artifacts** | Deliver all 12 raw files to report dir. | All 12 files verified present and byte-checked. | **PASS** | `handoff/inbox/report_027/` |

---

## 3. Automated QC Verification Gate Report (`c1_proof.qc.json`)

The master proof was verified against `Thresholds.from_preset("social")` with the autonomous `SPLIT` layout configuration:

```
QC REPORT  output/PHASE_3_CONVERSATIONAL/c1_proof.mp4
==============================================================================
  PASS  resolution                                1080x1920
  PASS  encoder                                   lavc62.28.101 libx264
  PASS  color metadata                            tagged
  PASS  loudness (LUFS)                           -14.3 LUFS
  PASS  true peak                                 -1.3 dBTP
  PASS  loudness range                            7.3 LU
  WARN  speech vs room tone                       7.2 dB
                                                  expected: >= 15.0 dB
                                                  -> Room tone is audible under the voice. Denoise before levelling.
  PASS  room resonance                            575 Hz +7.2 dB
  PASS  headroom (Top Panel CAM_A)                8.6% (min 5.1%)
  PASS  eyeline (Top Panel CAM_A)                 39.7%
  PASS  head clipping (Top Panel CAM_A)           0%
  PASS  back-of-head margin (Top Panel CAM_A)     8.0% (min 2.1%)
  PASS  lead room (Top Panel CAM_A)               62.6%
  PASS  framing stability (Top Panel CAM_A)       16.4px std
  PASS  headroom (Bottom Panel CAM_B)             11.9% (min 5.1%)
  PASS  eyeline (Bottom Panel CAM_B)              38.3%
  PASS  head clipping (Bottom Panel CAM_B)        0%
  PASS  back-of-head margin (Bottom Panel CAM_B)  27.3% (min 15.6%)
  PASS  lead room (Bottom Panel CAM_B)            60.0%
  PASS  framing stability (Bottom Panel CAM_B)    20.4px std
  PASS  headroom (per-panel)                      min 5.1%
  PASS  eyeline (per-panel)                       38.3%-39.7%
  PASS  head clipping (per-panel)                 0%
  PASS  back-of-head margin (per-panel)           min 2.1%
  PASS  lead room (per-panel)                     Top 62.6%, Bot 60.0%
  PASS  framing stability (per-panel)             max 20.4px std
==============================================================================
PASSED (26 checks, 0 failed, 1 warnings)
```

---

## 4. Detailed Per-Panel Geometry Measurements (`framing_per_panel.txt`)

```
=== PER-PANEL FRAMING MEASUREMENTS (c1 - SPLIT LAYOUT) ===
Evaluated against magnolia_interview preset targets and Commander Ruling 2 bands.

Top Panel: CAM_A (Dr. Fenton Lebon) — Verdict: PASS
  • Headroom           :  8.6% mean (min:  5.1%) [Target: 8.0%, Band: 5.0%-12.0%] -> PASS
  • Eyeline            : 39.7% mean             [Target: 33.0%, Band: 28.0%-40.0%] -> PASS
  • Top of Head Clip   :  0.0%                   [Tolerance: 0.0%] -> PASS
  • Back of Head Margin:  8.0% mean (min:  2.1%) [Target: >= 2.0% (sensor edge)] -> PASS
  • Lead Room          : 62.6% mean             [Band: 55.0%-67.0%] -> PASS
  • Center X           : 24.6% mean (std: 16.4px) [Threshold: < 40.0px] -> PASS

Bottom Panel: CAM_B (Dr. Saba Shabnam) — Verdict: PASS
  • Headroom           : 11.9% mean (min:  5.1%) [Target: 8.0%, Band: 5.0%-12.0%] -> PASS
  • Eyeline            : 38.3% mean             [Target: 33.0%, Band: 28.0%-40.0%] -> PASS
  • Top of Head Clip   :  0.0%                   [Tolerance: 0.0%] -> PASS
  • Back of Head Margin: 27.3% mean (min: 15.6%) [Target: >= 4.0%] -> PASS
  • Lead Room          : 60.0% mean             [Band: 55.0%-67.0%] -> PASS
  • Center X           : 65.5% mean (std: 20.4px) [Threshold: < 40.0px] -> PASS
```

---

## 5. Head-Size Matching & Resolution Bounding (`head_size_match.txt`)

Per **Ruling 5**, the physical limits and achievable ratios are documented without rationalization:
- **4K Source Raw Head Heights**:
  - Fenton: 347.5 px mean
  - Saba: 217.3 px mean
  - Raw Mismatch: **1.599x**
- **Crop Geometry & Scaling**:
  - Top Panel (Fenton): 1204 x 1070 crop scaled to 1080 x 960 (scale: 0.8972x)
  - Bottom Panel (Saba): 1058 x 940 crop scaled to 1080 x 960 (scale: 1.0213x)
  - Panel Zoom Ratio: **1.138x** (Cap: 1.50 -> PASS)
- **Final On-Screen Head Heights**:
  - Fenton: **311.8 px**
  - Saba: **222.0 px**
  - Measured Ratio: **1.405x** (reduced from 1.599x raw)
- **Source Resolution & Framing Ceiling Analysis**:
  - To force on-screen head heights within 1.10x (e.g. 311.8px vs 283.5px), Saba crop height would have to be reduced to $s\_ch \le 738	ext{px}$ (upscale $> 1.30	imes$).
  - However, sweeping $s\_ch$ below 940px physically violates Ruling 3 and standard framing bands:
    - At $s\_ch = 900	ext{px}$, Saba headroom mean rises to 12.06% (> 12.0% limit);
    - At $s\_ch = 880	ext{px}$, headroom mean is 12.28% and eyeline drops to 40.31% (> 40.0% ceiling);
    - At $s\_ch = 860	ext{px}$, headroom mean is 14.17% and eyeline is 42.88%.
  - Furthermore, upscaling beyond 1.05x from 4K crops introduces visible softening on fine facial textures.
  - Therefore, $s\_ch = 940	ext{px}$ is the exact physical boundary where all framing gates (headroom mean $\le 12.0\%$, min $\ge 5.0\%$, eyeline $\in [28.0\%, 40.0\%]$, lead room $\in [55.0\%, 67.0\%]$) and pristine sharpness are simultaneously satisfied.

---

## 6. Artifact Inventory (`handoff/inbox/report_027/`)

All 12 required raw artifacts are in place:
1. `layout_decision_c1.txt` (1,713 bytes): Autonomous engine report selecting SPLIT layout.
2. `framing_preset.txt` (3,337 bytes): Full YAML framing preset including Ruling 2 & 4 additions.
3. `framing_per_panel.txt` (1,267 bytes): Per-panel framing gate measurements showing both panels PASS.
4. `crops_c1.json` (240,521 bytes): Dynamic quantized per-frame crop boxes and hold metadata.
5. `c1_proof.qc.json` (9,011 bytes): Full 26-check QC report showing 0 failures.
6. `render_stdout_c1.txt` (4,704 bytes): Master render stdout log confirming clean run.
7. `face_tracks_c1.json` (257,754 bytes): Full 1200-frame raw face tracking data.
8. `framing_per_frame_dump.json` (966,496 bytes): Per-frame head top, eyeline, and headroom dump.
9. `edl_c1.json` (1,006 bytes): Edit decision list with shot boundaries.
10. `shots_metrics_c1.txt` (334 bytes): Shot duration, switch frequency, and pause cut metrics.
11. `speaker_timeline_c1.json` (129,652 bytes): Master speaker diarization timeline.
12. `head_size_match.txt` (2,049 bytes): Head size matching report and resolution cost analysis.
13. `report.md`: This comprehensive handoff document.

---

## 7. Delivery & Hard Stop

The handoff package is assembled. In accordance with the **Handoff Protocol** and the **HARD STOP RULE** in `AGENTS.md`:
1. `handoff/inbox/report_027/DONE` is touched as the final step.
2. `tools/handoff/bridge.py --once` is executed.
3. Execution halts immediately.
