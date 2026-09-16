# REPORT 026: Autonomous Layout Engine & Virtual Multi-Panel Proof (c1)

**Date**: 2026-09-11  
**Author**: Antigravity (Supervisor)  
**Target**: Conversational Segment c1 (945.0s – 985.0s, 1200 frames @ 30fps)  
**Status**: COMPLETE — Handoff Ready  

---

## 1. Executive Summary & Directive Execution

Per **Commander Directive prompt_026.txt** and **Commander Rulings 1–5**, this report delivers the fully functional **Autonomous Layout Engine** (`reelcore.layout.engine`), evaluates clip `c1`, deterministically selects the `SPLIT` layout based on measured face geometry and preset thresholds, and re-renders `c1_proof.mp4` as a stacked dual-panel vertical proof with independent per-panel framing QC gates.

All 9 required raw artifacts are assembled and verified in `handoff/inbox/report_026/`:
1. `layout_decision_c1.txt`: Autonomous decision report and measured geometry.
2. `framing_preset.txt`: Exact copy of `presets/framing/magnolia_interview.yaml`.
3. `framing_per_panel.txt`: Per-panel framing gate measurements (Top CAM_A, Bottom CAM_B).
4. `crops_c1.json`: Multi-panel crop metadata, panel dimensions, and auto-zoom verification.
5. `c1_proof.qc.json`: Automated QC gate report covering container, audio DSP, and per-panel framing.
6. `render_stdout_c1.txt`: Complete terminal log of rendering and verification.
7. `face_tracks_c1.json`: Full 1200-frame raw face tracking data (Ruling 4).
8. `framing_per_frame_dump.json`: Per-frame head-top-y, eyeline-y, and headroom dump (Ruling 4).
9. `edl_c1.json`: Virtual multi-cam shot list and edit decisions.

---

## 2. Autonomous Layout Engine Architecture (§1.5)

### 2.1 Decision Rule Implementation
The layout engine (`reelcore.layout.engine.evaluate_clip_layout`) operates autonomously on detected face tracks and framing presets without hardcoded literals:
1. **Measured Geometry Extraction**: Reads face bounding boxes, center coordinates, facing directions, and preset margin requirements (`back_of_head_margin: 0.04`, `lead_room_fraction`).
2. **Ceiling Definition**: Evaluates single 9:16 crop width limit from source height:
   Max Single Crop Width = Source Height * 0.5625 = 2160 * 9/16 = 1215.0 px
3. **Fits Single Crop Test**:
   - Computes raw combined bounding box across all subjects.
   - Computes combined span including required back-of-head margins and lead rooms.
   - Evaluates whether Combined Span <= 1215.0 px.
4. **Deterministic Selection Rules**:
   - **`FITS` (<= 1215px) + any subject count** -> `KEEP_ALL` (single wide frame, everyone visible, no switching; optimal for tight V-pattern setups).
   - **`DOES NOT FIT` (> 1215px) + exactly 2 faces** -> `SPLIT` (stacked 50/50 dual panels).
   - **`DOES NOT FIT` (> 1215px) + 3–4 faces** -> `GRID` (2x2 multi-panel).
   - **1 face only** -> `FILL` (speaker-tracked single crop).
5. **Manual Overrides**: Manifest-level overrides (`layout: SPLIT | FILL | KEEP_ALL | GRID`, `focus_subject: <id>`) are supported and take unconditional precedence over autonomous decisions.

### 2.2 Measured Numbers for Clip c1 (`layout_decision_c1.txt`)
- **Source Resolution**: 3840 x 2160 (4K UHD)
- **Single 9:16 Crop Width Ceiling**: 1215.0 px
- **Detected Subjects**: 2
  - **Dr. Fenton Lebon (`spk_a`)**: Face X span [73 .. 506] (width 433px), mean center (296.3, 1237.4), facing right, margin span [24.4 .. 992.0].
  - **Dr. Saba Shabnam (`spk_b`)**: Face X span [2298 .. 2606] (width 308px), mean center (2426.6, 1179.4), facing left, margin span [1569.0 .. 2654.6].
- **Combined Face Bounding Box**: X in [73 .. 2606] (raw span 2533 px)
- **Combined Span With Margins**: X in [24.4 .. 2654.6] (span 2630.2 px)
- **Fits Single Crop Test**: **FAIL** (2630.2 px > 1215.0 px, delta +1415.2 px / +116.5%)
- **Autonomous Choice**: **`SPLIT`** (2 subjects, exceeds single crop ceiling).

---

## 3. SPLIT Proof Execution & Visual Design

The proof reel was rendered to `c1_proof.mp4` implementing the prompt_026 quality requirements:
- **Dual Stacked Panels (50/50)**:
  - **Top Panel**: Dr. Fenton Lebon (CAM_A, `spk_a`), panel size 1080 x 960.
  - **Bottom Panel**: Dr. Saba Shabnam (CAM_B, `spk_b`), panel size 1080 x 960.
- **Live Panels**: Both panels read continuously from the 4K source on every frame; the inactive speaker is never frozen.
- **Active Speaker Visual Cue**: Subtle 4px `#FFE500` (Electric Yellow) border highlight drawn around the active speaker panel, synchronized to `speaker_timeline_c1.json`.
- **Panel Divider**: Clean 4px dark divider (`#1A1A1A`) at y in [958 .. 962].
- **Auto-Zoom Head-Size Matching**:
  - Fenton 4K crop height: 1100 px (scale 0.8724 -> 303px on-screen head size).
  - Saba 4K crop height: 960 px (scale 1.0000 -> 217px on-screen head size).
  - Zoom ratio: 1100 / 960 = 1.146 <= 1.25 (`max_panel_zoom` threshold satisfied).
- **Letterhead Overlays**: Positioned in respective upper/lower thirds with dark contrast backplate ensuring legibility against light background walls.
- **Kinetic Captions**: Centered over the divider line (y=936) inside a semi-transparent dark pill, completely clear of both subjects faces.

---

## 4. Per-Panel Framing QC Verification (`framing_per_panel.txt`)

Both panels were evaluated independently against the magnolia interview preset targets and Commander Ruling 2 bands:

```
=== PER-PANEL FRAMING MEASUREMENTS (c1 - SPLIT LAYOUT) ===
Evaluated against magnolia_interview preset targets and Commander Ruling 2 bands.

Top Panel: CAM_A (Dr. Fenton Lebon) — Verdict: FAIL
  • Headroom           :  8.5% mean (min:  5.4%) [Target: 8.0%, Band: 5.0%-12.0%] -> PASS
  • Eyeline            : 38.7% mean             [Target: 33.0%, Band: 28.0%-40.0%] -> PASS
  • Top of Head Clip   :  0.0%                   [Tolerance: 0.0%] -> PASS
  • Back of Head Margin:  7.8% mean (min:  2.0%) [Target: >= 4.0%] -> FAIL
  • Lead Room          : 63.6% mean             [Band: 35.0%-46.0%] -> FAIL
  • Center X           : 24.0% mean (std: 15.9px) [Threshold: < 40.0px] -> PASS

Bottom Panel: CAM_B (Dr. Saba Shabnam) — Verdict: FAIL
  • Headroom           : 12.1% mean (min:  5.1%) [Target: 8.0%, Band: 5.0%-12.0%] -> FAIL
  • Eyeline            : 37.9% mean             [Target: 33.0%, Band: 28.0%-40.0%] -> PASS
  • Top of Head Clip   :  0.0%                   [Tolerance: 0.0%] -> PASS
  • Back of Head Margin: 27.2% mean (min: 15.5%) [Target: >= 4.0%] -> PASS
  • Lead Room          : 60.4% mean             [Band: 55.0%-67.0%] -> PASS
  • Center X           : 65.8% mean (std: 23.5px) [Threshold: < 40.0px] -> PASS
```

### Honest Disclosure of Failures (Strict Adherence to Prompt 026 & Ruling 2)
Per Commander instructions: *"If SPLIT cannot satisfy the framing bands from this source, say so with measured numbers instead of forcing a pass"* and *"a value outside its band is a FAIL. Never PASS with a prose excuse"*:

1. **Top Panel Back-of-Head Margin (Measured Min 2.0% vs Target >= 4.0% -> FAIL)**:
   - **Root Cause**: In the 4K raw source, Dr. Fenton sits at x in [64 .. 506]. His face is within 64 pixels of the physical sensor edge (X=0). In a 1238px wide crop (X=0), scaling to 1080px places his skull back at 2.0% from the panel edge on frames where he leans back. Because X=0 is the hardware sensor boundary, no further leftward crop is physically possible.
2. **Top Panel Lead Room (Measured 63.6% vs Ruling 2 Band [0.35, 0.46] -> FAIL)**:
   - **Root Cause**: The band [0.35, 0.46] in Ruling 2 was established for a narrow single 9:16 crop (W=780px). In a 9:8 split panel (W/H = 1.125), maintaining vertical framing (H=1100px) dictates a crop width of 1238px. With Fenton situated on the left, the horizontal space in front of him is 1238 - 451 = 787px, which scales to 63.6% lead room in the panel.
   - **Recommendation**: Update preset with a dedicated split-panel band for CAM_A of [0.55, 0.67], matching Saba lead room.
3. **Bottom Panel Headroom (Measured Mean 12.1% vs Band [0.05, 0.12] -> FAIL)**:
   - **Root Cause**: Dr. Saba exhibits natural vertical movement spanning 70px across the 40 seconds. While her minimum headroom is preserved at 5.1% >= 5.0%, the mean sits at 12.1%, exceeding the 12.0% ceiling by 0.1%.

---

## 5. Compliance with Commander Rulings 1–5

1. **Ruling 1 (Step 0 Formally Closed)**: Step 0 is closed and waived from re-proof. No Step 0 literals exist in the codebase.
2. **Ruling 2 (Lead Room Bands & Zero False Passes)**: Named per-speaker bands are configured in `presets/framing/magnolia_interview.yaml`. Measurements outside bands are reported strictly as `FAIL`.
3. **Ruling 3 (Preset Seeds vs Dynamic Measurements)**: Preset seeds in YAML are explicitly documented as seeds (`presets/framing/magnolia_interview.yaml: speaker_search_seeds`). All report figures are measured numbers.
4. **Ruling 4 (Artifact Auditing)**: Both `face_tracks_c1.json` and `framing_per_frame_dump.json` (per-frame head-top-y, eyeline-y, headroom fraction for both subjects across all 1200 frames) are included in the report package.
5. **Ruling 5 (SPLIT Conflict Resolution)**: SPLIT provides dedicated panels for both subjects, eliminating single-crop face switching while maintaining live dual presence.

---

## 6. Handoff Checklist
- [x] Autonomous Layout Engine implemented (`reelcore/src/reelcore/layout/engine.py`)
- [x] Preset updated with layout parameters (`presets/framing/magnolia_interview.yaml`)
- [x] Fits Single Crop Test executed and logged (`layout_decision_c1.txt`)
- [x] Dual-panel SPLIT layout rendered (`c1_proof.mp4`)
- [x] Per-panel framing QC evaluation executed (`c1_proof.qc.json`, `framing_per_panel.txt`)
- [x] Per-frame head top and eyeline dump generated (`framing_per_frame_dump.json`)
- [x] All 9 raw artifacts copied to `handoff/inbox/report_026/`
- [x] Hard stop rule in `AGENTS.md` observed
