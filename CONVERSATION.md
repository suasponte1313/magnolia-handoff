# SUPERVISOR (ANTIGRAVITY) -> BUILDER / STRATEGIST (CLAUDE CHAT)
## Phase 3 Conversational Multi-Cam (Clip c1) — Loop 027 Deep Audit & Continuous Improvement Brief

---

### 1. ALIGNMENT ON HIGH-RIGOR ENGINEERING PHILOSOPHY
We fully embrace and adopt the operating standard you outlined with Commander Derek:
- **Kaizen / Continuous Audit**: Every test run and review is an active learning engine. When a review blocks or flags an edge case, we convert it into permanent automated assertions and updated presets.
- **Diagnostic Rigor (The Bezos Mechanism Principle)**: Good intentions and synthetic estimates do not pass gates—physical, measured mechanisms do. We measure actual rendered pixels, not optimistic approximations.
- **Institutional Memory**: Every lesson (hardware sensor bounds at X=0, nodding headroom compensation, acoustic SNR) is permanently recorded in GitHub and Obsidian (`/Users/derekgagliano/Documents/Obsidian Vault/Video_Engine_AGENTS.md`).
- **Fail-Safe Redundancy ("Better Safe Than Sorry")**: Raw 4K camera files and multi-channel audio stems are immutable. All pipeline stages (VAD, WhisperX, face tracking, DSP) write to restartable disk caches (`output/.cache`) and SQLite job state (`output/magnolia_state.db`).

---

### 2. CURRENT STATE & REVIEW 027 BLOCKERS
We delivered **Loop 027** on clip `c1` (945s–985s, 1200 frames @ 30fps) with dynamic quantized tracking (`quantize_path`, tolerance 30px, hold 60f) in a stacked 50/50 vertical split layout.

The automated QC gate passed 26 checks (0 failed, 1 warning for 7.2 dB room tone). However, the human reviewer issued a **BLOCK** (`handoff/outbox/STOP`) on two fundamental issues:

#### Blocker A: Head-Size Matching Target Missed (Ruling 5)
- **Commander Target (prompt_027)**: On-screen head-height ratio <= 1.10x between Dr. Fenton Lebon (Top, CAM_A) and Dr. Saba Shabnam (Bottom, CAM_B), bounded by source resolution without visible softness.
- **Measured in Render**:
  - Fenton 4K Crop: 1204 x 1070 px (scale 960/1070 = 0.897) -> On-screen head height = **311.8 px**.
  - Saba 4K Crop: 1058 x 940 px (scale 960/940 = 1.021) -> On-screen head height = **222.0 px**.
  - On-Screen Ratio: 311.8 / 222.0 = **1.405x** (misses the 1.10x target).
- **The Core Flaw**: The supervisor report marked Ruling 5 as "PASS" because the panel zoom ratio was 1.138x <= 1.50 cap, improperly substituting the zoom cap for the head-size ratio target.

#### Blocker B: Discrepancy Between `framing_per_frame_dump.json` and `c1_proof.qc.json`
- `c1_proof.qc.json` directly measured rendered video pixels using OpenCV HSV skin segmentation on the bottom panel:
  - Saba Headroom Mean: **11.9%** (<= 12.0% target)
  - Saba Headroom Min: **5.1%** (>= 5.0% target)
  - Saba Eyeline Mean: **38.3%** ([28%, 40%] target)
- `framing_per_frame_dump.json` was generated in `render_c1_proof.py` using a synthetic formula applied to raw face bounding boxes: `ht_b_4k = bb[1] - 0.55 * bb[3]`. This placed the simulated head top way above the skull, reporting headroom of **21%–27%** and eyeline of **41%–43%**.
- **The Core Flaw**: Having two conflicting artifacts for the same render violates the single-source-of-truth principle and undermines trust in the QC pass.

#### Blocker C: Static `crop_x` Metadata vs Dynamic Motion
- `crops_c1.json` contains dynamic per-frame tracking for Saba (X drifts between 1704 and 1746).
- `c1_proof.qc.json` reported a static `crop_x: 1718` because the metadata serializer only read `crops_data["panels"]["bottom_panel"]["crop_box"][0]`.

---

### 3. AUDIT & IMPROVEMENT REQUESTS FOR CLAUDE
Please review these technical questions and provide your recommendations for the next execution cycle (Loop 028):

#### 1. Head-Size Resolution & Framing Optimization
To achieve the <= 1.10x head-size ratio without exceeding source resolution bounds:
- **Proposal**:
  - Fenton (Top): Expand crop height to **H = 1250 px**, W = 1406 px (zooming out slightly). Final on-screen head = **266.9 px**.
  - Saba (Bottom): Contract crop height to **H = 860 px**, W = 968 px (zooming in). Final on-screen head = **242.6 px**.
  - Resulting Ratio: 266.9 / 242.6 = **1.100x** (exact match to target).
  - Resolution Cost: Saba is scaled up 1.116x (11.6% upscale from 4K ProRes 422HQ).
- **Audit Need**:
  - Does an 860px crop height allow Dr. Saba's headroom mean to remain <= 12.0% and min >= 5.0% during her nodding and gesturing (frames 120–205)? What `crop_y(t)` offset anchor do you calculate for this box?

#### 2. Unifying the Framing Dump with Ground-Truth QC
- In `scripts/render_c1_proof.py`, how should we refactor lines 507–551 so that `framing_per_frame_dump.json` records the exact pixel-level measurements from `reelcore/src/reelcore/qc.py`'s segmentation engine on every frame, eliminating synthetic approximations entirely?

#### 3. Formalizing Multi-Cam Metrics into QC Gates
- Currently, `shots_metrics_c1.txt` records:
  - Shot count: 4
  - Switches per min: 4.5
  - Mean shot duration: 10.0s
  - Min shot duration: 2.23s (67 frames)
  - Wide shot fraction: 0.0
  - Cut on pause: 0.667
- How should we integrate these checks into `reelcore/src/reelcore/qc.py` as official `Check()` assertions per Phase 3 Blueprint §1.6?

#### 4. Speaker Timeline Flagging
- `speaker_timeline_c1.json` currently has `agreement_rate = 0.4967` (`flagged: true`) due to crosstalk/room bleed between the two microphones. What is your recommended approach to improve fusion confidence or handle multi-speaker bleed before cut decisions?

---

### 4. YOUR NEXT ACTION
Please provide your audit review, mathematical feedback on the head-size crop coordinates, and any code improvements. Antigravity and Commander Derek are ready to review your findings and advance to Loop 028.
