# Review — loop 29 — Phase 3 §1 — Commander Rulings 1-5 execution on c1
**Verdict: BLOCK**  ·  ⛔ STOP

### Verified from artifacts
- head_size_ratio_max: 1.30 confirmed in framing_preset.txt
- measured head-size ratio 1.285x confirmed in crops_c1.json (head_size_matching.ratio: 1.285, ratio_satisfied: true) and head_size_match.txt
- 0 unmeasured frames (200/200 measured) confirmed in unmeasured_frames.txt
- Fenton (Top Panel) headroom 7.1% (min 5.8%, max 9.2%) confirmed in c1_proof.qc.json
- Fenton eyeline 38.2% (min 34.1%, max 39.6%) confirmed in c1_proof.qc.json — PASS
- Fenton lead room 62.6%, back margin min 2.1%, skull clip 0% confirmed — PASS
- Saba (Bottom Panel) headroom 7.8% (min 5.2%, max 9.9%) confirmed — PASS
- Saba eyeline 36.8% mean but max 44.8% confirmed in c1_proof.qc.json — FAIL (exceeds 40% ceiling)
- Gesture frames 138-168 show elevated eyeline (0.4332-0.4481) in framing_per_frame_dump.json, confirming AG's explanation
- Multi-cam: 4 shots, 4.5 switches/min, 10.0s mean duration confirmed in shots_metrics_c1.txt and edl_c1.json
- QC gate passed: false (2 eyeline failures) confirmed in c1_proof.qc.json

### Unverified / suspicious
_(none)_

### Blocking
- Eyeline max 44.8% exceeds preset ceiling 40% — QC gate formally fails. AG cannot self-approve; Commander must decide path forward.

### Carried (non-blocking)
- speech_vs_room_tone WARN 7.2 dB (target >= 15 dB) — denoise before final delivery
- cut_on_pause WARN 66.7% (target >= 75%) — non-blocking, 2 of 3 cuts aligned

## Why the loop stopped (Commander must clear)
AG executed Rulings 1-5 correctly and honestly reported a QC FAIL per SELF-APPROVAL BAN. The eyeline max 44.8% during gesture frames (138-168) exceeds the 40% preset ceiling. This is a geometry/physics constraint, not an AG error. Commander must choose: (A) accept gesture-frame eyeline exception — mean 36.8% is well-centered, only transient peaks exceed; (B) expand eyeline_max_fraction to 0.45 for split layout in preset; or (C) increase Saba crop H to 960+ accepting head-size ratio > 1.30. All three involve tradeoffs only the Commander can adjudicate.

## Next prompt for AG  [RECOMMENDED — pending your approval]
```
COMMANDER RULING REQUIRED — Eyeline max 44.8% (gesture frames 138-168) exceeds 40% ceiling. All other framing passes. Options:

(A) ACCEPT gesture-frame exception — update preset with `eyeline_max_fraction_gesture_tolerance: 0.45` or document this as acceptable transient variance. Mean eyeline 36.8% is well-centered.

(B) EXPAND split-layout eyeline_max to 0.45 — update `presets/framing/magnolia_interview.yaml` to allow wider band for split layout.

(C) INCREASE Saba crop H to 960+ — tighter framing, but head-size ratio will exceed 1.30x, requiring another preset relaxation.

Please select (A), (B), or (C) with any modifications, or provide an alternative ruling.
```