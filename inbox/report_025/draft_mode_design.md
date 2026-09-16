# Magnolia Video Engine: Draft Mode Architecture & Design Specification
**Document Version:** 1.0.0  
**Author:** Antigravity (Magnolia OS Supervisor)  
**Commander:** Derek Gagliano  
**Status:** Approved Design Specification (Phase 3 Extension)  
**Implementation Target:** Future Loop (Design Only in Loop 25)  

---

## 1. Executive Summary & Problem Statement

### 1.1 The Operational Bottleneck
Currently, producing a conversational 9:16 vertical reel from a long-form 4K multicam interview requires:
1. Manual or semi-manual discovery of dialogue segments.
2. Full multi-stage audio DSP mastering (spectral denoising, room resonance notch filtering, gate/compressor dynamics, two-pass LUFS normalization).
3. Slow, broadcast-quality software encoding (`libx264` CRF 18, Lanczos resampling, BT.709 colorimetry).
4. Rigorous automated QC gating.

When applied across an entire 60-to-90-minute episode, executing the full master audio DSP and software video encode on dozens of potential candidate moments is computationally expensive and introduces unnecessary latency into the creative editorial review cycle.

### 1.2 The Solution: Draft Mode
**Draft Mode** is a high-throughput, preset-driven candidate discovery and preview subsystem. It scans an entire episode, automatically extracts and ranks the highest-potential conversational clips, applies preset-governed multi-camera switching and 9:16 framing geometry, and renders lightweight, low-latency draft previews. 

Commander Derek Gagliano can review the ranked draft reels, evaluate the narrative hooks, visually inspect or nudge the framing and color grade, and with a single command promote approved drafts to the **Final Master Pipeline**. High-computation audio restoration and mastering is completely decoupled into a dedicated downstream pass.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        FULL EPISODE 4K FOOTAGE                          │
│               (Video: 3840x2160 30fps | Audio: 48kHz Stereo)            │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                     STAGE 1: EPISODE INDEXING & DIARIZATION             │
│   • WhisperX word-level transcription + speaker diarization             │
│   • MediaPipe face tracking & cranial landmark registration             │
│   • Acoustic energy & speech cadence profiling                          │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                     STAGE 2: CANDIDATE CLIP EXTRACTION                  │
│   • Rule-based pause boundary snapping (midpoint of >=250ms pauses)     │
│   • Duration clamping (30s to 60s vertical reel sweet spot)             │
│   • Complete thought & conversational turnaround detection              │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                     STAGE 3: MULTI-DIMENSIONAL RANKING                  │
│   • Hook Score (opening 3-5s energy, lexical punch, speaker prompt)     │
│   • Engagement & Turnover (speaker exchange dynamics, reaction shots)   │
│   • Visual Tracking Stability (cranial margin clearance, no occlusions) │
│   • Acoustic SNR Baseline                                               │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                     STAGE 4: ULTRA-FAST DRAFT RENDER                    │
│   • Fast proxy hardware encode (VideoToolbox / ultrafast x264 CRF 23)   │
│   • Full 9:16 framing geometry & EDL cuts applied                       │
│   • Lightweight single-pass audio levelling (bypassing heavy DSP)       │
│   • Render latency: ~3 to 5 seconds per 45s reel                        │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                     STAGE 5: COMMANDER REVIEW INTERFACE                 │
│   • Interactive CLI / lightweight Web UI review surface                 │
│   • Rapid scrub & visual framing inspection                             │
│   • Intuitive parameter overrides (headroom nudge, eyeline, LUT, cuts)  │
│   • State machine: PENDING → ADJUSTED → APPROVED → MASTERED             │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │ (Approved Drafts)
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                     STAGE 6: FINAL MASTER & AUDIO POLISH                │
│   • Broadcast software encoding (libx264 CRF 18, Lanczos, BT.709 tags)  │
│   • Studio DSP chain (denoise, notch filters, optical limiter, -14 LUFS)│
│   • Full QC Gate verification (c1_proof.qc.json)                        │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Pipeline Architecture & Detailed Component Design

### 2.1 Stage 1: Episode Ingestion & Indexing
The full episode runs through the existing foundational analyzers once:
- **Audio Extraction:** High-resolution 48kHz mono dialogue track extracted to `temp/episode_audio.wav`.
- **Transcription & Diarization:** `WhisperX` generates word-level alignment with precise start/end timestamps and diarized speaker assignments (`spk_a`, `spk_b`).
- **Visual Landmark Tracking:** Face and cranial tracking runs over the raw 4K source at a sampled interval (every 3 frames, interpolated), generating global continuous tracks for all participants (`face_tracks_global.json`).

### 2.2 Stage 2: Candidate Clip Boundary Detection
To eliminate abrupt cuts mid-word or mid-sentence:
- **Pause Snapping:** All candidate boundaries must snap to the **midpoint of a detected acoustic pause** ($\ge 250\text{ms}$) identified from word timestamps.
- **Duration Envelopes:**
  - Minimum clip length: 25.0 seconds (750 frames @ 30fps).
  - Target clip length: 40.0 to 55.0 seconds.
  - Maximum clip length: 65.0 seconds.
- **Semantic Completeness:** Candidates are identified using sentence punctuation, dialogue turnarounds, and conversational closures (avoiding trailing thoughts).

### 2.3 Stage 3: Multi-Dimensional Ranking Engine
Each candidate segment $C_i$ is evaluated by a composite ranking function yielding a normalized score $S(C_i) \in [0.0, 1.0]$:

$$S(C_i) = w_{\text{hook}} S_{\text{hook}} + w_{\text{dynamic}} S_{\text{dynamic}} + w_{\text{visual}} S_{\text{visual}} + w_{\text{audio}} S_{\text{audio}}$$

#### A. Hook Score ($S_{\text{hook}}$, weight = 0.35)
Evaluates the first 3.0 to 5.0 seconds of the candidate:
- **Acoustic Energy Influx:** Ratio of initial 3s RMS energy relative to the segment mean.
- **Lexical Salience:** Keyword presence (e.g. medical revelations, provocative questions, high information density words).
- **Prompt Turn:** Bonus if the segment begins with a compelling question or counter-intuitive assertion.

#### B. Conversational Dynamic Score ($S_{\text{dynamic}}$, weight = 0.30)
Evaluates the multi-camera editorial rhythm:
- **Turnover Rate:** Rewards segments featuring an active speaker followed by a meaningful listener reaction and dialogue handoff (2 to 5 cuts per minute).
- **Speaker Point Retention:** Penalizes rapid bouncing while a speaker is elaborating a point (enforcing Section C: "Hold on the speaker while making a point").
- **Reaction Validity:** Verifies listener cut occurs during speaker pauses rather than during active dialogue.

#### C. Visual Framing Feasibility Score ($S_{\text{visual}}$, weight = 0.20)
Pre-validates that the 4K geometry can cleanly fulfill Section A & D framing targets:
- **Sensor Feasibility:** Verifies cranial tops and chest boundaries stay within the physical 2160 vertical limit without floor clipping.
- **Occlusion Penalty:** Penalizes frames where hands, mugs, or props obscure the face.
- **Motion Stability:** Evaluates cranial motion standard deviation; rewards stable seated dialogue.

#### D. Audio Clarity Baseline ($S_{\text{audio}}$, weight = 0.15)
- Evaluates raw SNR ($> 15\text{dB}$) and absence of transient broadband clipping or severe room ringing.

---

## 3. Fast Draft Rendering vs. Final Master Render

A central principle of Draft Mode is **extreme preview velocity**. The table below defines the explicit architectural separation between Draft and Final passes:

| Parameter | Fast Draft Render Mode | Final Master Render Mode | Rationale |
| :--- | :--- | :--- | :--- |
| **Video Encoder** | `h264_videotoolbox` (Hardware) or `libx264` `-preset ultrafast` | `libx264` `-preset slow -crf 18` | Draft prioritizes instant playback (3-5s); Master ensures pristine visual fidelity. |
| **Resolution** | 1080x1920 (or 720x1280 preview) | 1080x1920 Full HD Vertical | Exact framing preview at native aspect ratio. |
| **Scaling Filter** | `bilinear` / `bicubic` | `Lanczos4` + adaptive unsharp mask | Sharpest edge reconstruction for 4K downsampling. |
| **Color Grading** | Fast 1D Tone Curve approximation | Full Rec.709 colorimetry + LUT mapping | Immediate tone inspection without complex color transforms. |
| **Audio Processing** | Single-pass fast EBU R128 normalize (-14 LUFS) | Full Studio DSP Chain (denoise, notch, optical compressor, limiter) | Bypasses slow spectral processing during drafting. |
| **Burned Overlays** | Draft watermark + TC + Letterhead + Captions | Production Letterhead + Kinetic Captions (no watermark) | Clear provenance during editorial review. |
| **Execution Time** | **~3 - 5 seconds** (per 45s reel) | **~35 - 50 seconds** (per 45s reel) | **10x speedup** for rapid candidate iteration. |

---

## 4. Decoupled Audio Polish Pass (Detailed Rationale)

In Phase 1 and 2, audio processing was tightly coupled to video rendering. In a multi-candidate drafting workflow, this is inefficient:
1. **Computational Overhead:** `noisereduce` spectral profiling, recursive room-resonance FFT analysis, and lookahead dynamic limiting require significant CPU time. Performing this on 15 candidate drafts consumes 3-4 minutes of unnecessary processing.
2. **Creative Isolation:** Framing, crop positioning, and shot pacing are visual creative decisions. An editor or commander does not need studio-mastered audio to decide whether Dr. Saba's reaction shot should hold for 2.0s or 2.5s.
3. **Non-Destructive Final Mastering:** Once the visual EDL is locked by the Commander, the final audio polish pass runs directly on the chosen segment with calibrated studio parameters:
   - High-pass filtering at measured vocal cutoff.
   - Surgical notch filtering on detected room modes (e.g., 200 Hz / 400 Hz peaks).
   - Dynamic dialogue leveling and transparent optical compression.
   - True Peak limiting to $\le -1.0\text{ dBTP}$ and integrated loudness to $-14.0\text{ LUFS}$.

---

## 5. Commander Review Interface & Control Surface

### 5.1 Candidate Manifest Structure (`drafts/candidates.yaml`)
Draft mode automatically outputs a candidate manifest documenting ranked clips:

```yaml
episode_id: "Dallas1"
source_video: "input/raw_part_2_Dallas1.mov"
candidates:
  - id: "draft_001"
    rank: 1
    composite_score: 0.924
    start_time: 945.20
    end_time: 985.40
    duration_s: 40.20
    topic_summary: "Nano NAD cellular uptake & clinical response"
    scores:
      hook: 0.95
      dynamic: 0.91
      visual: 0.94
      audio: 0.88
    review_status: "PENDING"  # PENDING | APPROVED | REJECTED | ADJUSTED
    draft_mp4: "drafts/draft_001_preview.mp4"
    config_file: "drafts/draft_001_config.yaml"
```

### 5.2 Per-Clip Review & Tuning Config (`drafts/draft_001_config.yaml`)
The Commander can inspect or modify framing and color directly in an isolated YAML config:

```yaml
draft_id: "draft_001"
shot_overrides:
  - shot_index: 1
    camera: "CAM_A"
    subject: "spk_a"
    crop_nudge_x_px: 0       # horizontal framing adjustment (+/- px)
    crop_nudge_y_px: -10     # vertical headroom adjustment (+/- px)
    push_in_scale: 1.04      # conversational push-in scale
  - shot_index: 2
    camera: "CAM_B"
    subject: "spk_b"
    hold_duration_s: 2.5     # adjust reaction shot length

color_grade:
  preset: "broadcast_warm"
  saturation_scale: 1.20
  highlight_rolloff: true

captions:
  style: "magnolia_clean"
  highlight_color: "#FFE500"
  vertical_position_y: 1400
```

### 5.3 Commander CLI Workflow
The Commander controls the workflow with clean CLI commands:
```bash
# 1. Generate and rank top 5 drafts from Dallas1 episode
reelcore draft generate --input input/raw_part_2_Dallas1.mov --top 5

# 2. Inspect review manifest
reelcore draft status

# 3. Preview draft 001 on local display
open drafts/draft_001_preview.mp4

# 4. Apply any adjustments from drafts/draft_001_config.yaml and re-render draft preview
reelcore draft preview --id draft_001

# 5. Approve draft for master delivery
reelcore draft approve --id draft_001

# 6. Execute Master Render & Studio Audio Polish on approved drafts
reelcore render --approved --preset social
```

---

## 6. Verification & Quality Gates

Promoting a draft to the Master Render triggers the full QC gate (`reelcore.qc`) with zero manual overrides permitted:
- **Framing QC:** Headroom in $[0.05, 0.12]$ (enforced on per-frame minimum); Eyeline in $[0.28, 0.40]$; Back-of-head margin $\ge 0.04$; Head clipping $= 0.0\%$; Stability drift $< 25\text{px}$.
- **Audio QC:** Integrated Loudness $-14.0 \pm 1.0\text{ LUFS}$; True Peak $\le -1.0\text{ dBTP}$; LRA $\le 11.0\text{ LU}$; Speech-to-Room SNR $\ge 15.0\text{ dB}$.
- **Delivery QC:** BT.709 color tagging; Software libx264 encoding; Exact 1080x1920 vertical canvas.

---

## 7. Implementation Roadmap

1. **Loop 26:** Candidate Extraction & Ranking Engine (`reelcore/src/reelcore/draft/ranker.py`).
2. **Loop 27:** Fast Draft Proxy Renderer (`reelcore/src/reelcore/draft/render.py`).
3. **Loop 28:** Commander CLI Control Surface (`reelcore draft generate / approve`).
4. **Loop 29:** Full Automated Episode End-to-End Test.
