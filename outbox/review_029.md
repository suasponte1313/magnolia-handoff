# Review — loop 29 — Phase 3 §1.3 (multi-cam proof c1) — Step 0 framing gate
**Verdict: BLOCK**  ·  ⛔ STOP

### Verified from artifacts
- QC hard gate FAILED: c1_proof.qc.json 'passed': false, render_stdout_c1.txt 'FAILED (32 checks, 3 failed, 2 warnings)'. Not a pass — cannot advance.
- Eyeline FAIL, Top Panel CAM_A (Fenton): qc.json eyeline_max 0.404 > 0.40 ceiling; confirmed in framing_per_frame_dump.json (e.g. frame 1182 spk_a 0.404, frame 216 0.4019).
- Eyeline FAIL, Bottom Panel CAM_B (Saba): qc.json eyeline_max 0.4481 > 0.40; confirmed in dump (frame 168 spk_b 0.4481, frame 162 0.4413, frame 156 0.4272).
- AG honestly reported both eyeline misses as FAIL per the SELF-APPROVAL BAN rather than re-labeling the mean — credited.
- Commander Ruling 1 (prompt_029.txt) authorizes head_size_ratio_max: 1.30 and Fenton H=1070; framing_preset.txt matches. The config change is ordered, NOT self-authored. Measured ratio 1.285x (crops_c1.json) is under the ordered 1.30x — reported, not forced.
- Ruling 2 dump fix landed: qc.py records real per-frame values; unmeasured_frames.txt shows 0/200 unmeasured, and framing_per_frame_dump.json carries 'measured': true per frame with noisy (non-default) values — not the fabricated 0.08/0.33/0.60 defaults.
- 'flagged': false at agreement_rate 0.4967 is authorized, not a defect: speaker.py lines 79/624 hardcode flagged=False as retired per Ruling 5 / AGENTS.md (mouth-motion weight 0.0).
- Multi-cam metrics real: shots_metrics_c1.txt / edl_c1.json = 4 shots, 3 switches (4.5/min), min 2.23s, mean 10.0s, wide 0.0%, all traceable to the EDL.

### Unverified / suspicious
- Saba's 44.8% eyeline is likely a SEGMENTATION-MERGE artifact, not a true framing miss: at frame 168 headroom is 9.94% (box top HIGH in frame) yet eyeline is 44.8% (eyes LOW) simultaneously — the signature of face+chest skin merging into one tall blob, so eyeline_offset (0.35 * box_height from top) lands too low. AG asserts this ('395px box, chest groups in') but supplied no qc.py box-construction evidence proving connectedComponentsWithStats largest-blob isolates the face on gesture frames 138-168 / 888-900.
- unmeasured_frames.txt reports flat 100% measured (0/200 both panels). Plausible given the dump shows noisy values, but AG did not paste the qc.py assignment showing null/measured:false is actually emitted on an invalid frame — accepted provisionally, verify if segmentation is ever questioned again.
- report.md is silent on the bridge (Ruling 5 required the verbatim bridge.py error) and on the mirror push (Ruling 6). No bridge stdout/error artifact in report_029.

### Blocking
- QC does not pass. The §1.3 proof cannot be accepted with a failing gate.
- Fenton eyeline 40.4% > 40% ceiling is a direct product of Commander-ordered H=1070 (Ruling 1) — a genuine constraint collision the Commander must adjudicate, OR AG must prove a crop_y-only fix that holds eyeline max <= 40% with headroom still in [5%,12%] and H=1070 unchanged.
- Saba eyeline 44.8% must be resolved at its source: prove whether it is a real framing miss or a chest-blob merge in qc.py. If it is a merge, the eyeline number is measured off a bad box and the QC is wrong, not the crop.

### Carried (non-blocking)
- WARN cut on pause 66.7% (< 75% target), 2 of 3 cuts on pauses — non-blocking, carry into §1.3 acceptance.
- WARN speech vs room tone 7.2 dB (< 15 dB) — denoise-before-levelling item, carried from prior loops.
- Bridge error text (Ruling 5) and mirror-push confirmation (Ruling 6) still owed.

## Why the loop stopped (Commander must clear)
Two of the three eyeline failures cannot be resolved by AG alone: Fenton's 40.4% overage is caused by Commander Ruling 1's mandated H=1070, and AG's tabled remedies (expand eyeline band to 45%, or raise head-size ratio > 1.35) are changes to standing preset config that only the Commander may authorize. This is a constraint collision needing the Commander's judgment, not a mechanical check.

## Next prompt for AG  [RECOMMENDED — pending your approval]
```
AG — STOP. QC failed (passed:false, 3 FAILs). Do NOT touch any preset band or head_size_ratio — those are Commander decisions, not yours. Two tasks, raw output only, no tables:

1. SABA 44.8% — prove it's real, not a chest merge. Paste the qc.py block that builds the box used for the bottom-panel eyeline (the connectedComponentsWithStats / largest-blob lines) verbatim. Then for spk_b frames 138,150,162,168 and 888,894,900, paste the raw (x,y,w,h) of the exact box QC measured eyeline from — one line per frame, no prose. If the box height balloons on those frames vs neighbors, the eyeline is measured off face+chest and the QC is wrong, not the crop — say so.

2. FENTON 40.4% — attempt a crop_y-only tightening (H stays 1070, ratio untouched) to pull per-frame eyeline max <= 40% while headroom stays in [5%,12%]. Re-run QC. Paste the raw eyeline min/mean/max and headroom min/mean/max for CAM_A straight from the new c1_proof.qc.json — no reformatting.

3. Paste the exact bridge.py error text (Ruling 5) and confirm the mirror push ran (Ruling 6). Your report_029 was silent on both.

Do NOT re-render as 'passing' by moving a band. Produce artifacts 1-3, DONE last, then STOP. If after (1) Saba is a measurement artifact and after (2) Fenton still can't clear 40% on crop_y alone, report the residual as FAIL with the raw numbers and WAIT — the Commander decides whether the band moves.
```