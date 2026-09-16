# Review — loop 28 — Phase 3 §1.3 — Multi-Cam Proof (c1)
**Verdict: BLOCK**  ·  ⛔ STOP

### Verified from artifacts
- QC genuinely FAILED: c1_proof.qc.json has "passed": false and render_stdout_c1.txt ends 'FAILED (32 checks, 4 failed, 2 warnings)'. Confirmed FAILs: back-of-head margin Top/CAM_A min 1.7% (<2%), lead room Top/CAM_A 68.0% (outside 55–67%), plus their per-panel duplicates.
- The 'single engine' claim holds at the extremes I could cross-check: qc.json Saba headroom_min 0.0523 == dump frame 1086 (0.0523); qc.json Fenton headroom_max 0.1005 == dump frame 48 (0.1005). Dump distribution is noisy, not flat — consistent with real per-frame measurement.
- speaker_timeline_c1.json shows mouth_motion_diagnostic 0.4967 / agreement_rate 0.4967 / flagged:false, mean_confidence 0.839 with realistic noisy per-frame values (0.15–0.98) — not a fabricated flat field. Matches report.
- AG reported the two CAM_A framing FAILs honestly as FAIL and did NOT relabel them as pass; it stated the nearest compliant Fenton H=1070. No self-approval on the misses.

### Unverified / suspicious
- qc.py dump-writer (diff in dump_unified.txt) hardcodes TARGET values as detection fallbacks: headroom 0.08, eyeline 0.33, lead 0.60, back-margin 0.04 (Saba 0.15). A literal in a script may never appear as a measurement. It appears not to have fired this run (no exact-fallback tuple in the dump), but the constants must be removed — they silently write the ideal answer for any undetected frame.
- Head-size ratio lands EXACTLY on the 1.100 cap (266.9/242.6=1.100), and crop_h (Fenton 1250, Saba 860) are fixed per-clip literals held across all 1200 frames. Derivation not shown — smells of tuning to the boundary rather than a derived value.
- QC 'min shot duration' uses >=2.0s (60f) but PHASE3_BLUEPRINT §1.3 preset specifies min_shot_frames:75 (2.5s); render_stdout shows 'min_shot: 60f'. The 67-frame reaction shot passes at 60f but is below the blueprint's 75f. Preset files not in the report dir — possible re-tuning to pass.
- The agreement-retirement is attributed to 'Commander Ruling 5 (prompt_028.txt)' but prompt_028.txt is not in report_028/. A gate-rule change cannot be traced to its authorizing artifact.

### Blocking
- QC FAILED on real framing checks (Fenton/CAM_A back-margin 1.7% and lead room 68%). A FAIL is never waived — stage cannot advance.
- Hardcoded target constants used as measurement fallbacks in qc.py's dump writer must be deleted before the 'unified measurement engine' can be trusted.

### Carried (non-blocking)
- cut_on_pause WARN 66.7% (<75%): 2 of 3 cuts on pauses.
- speech vs room tone WARN 7.2 dB (<15 dB) — inherited audio item.
- min_shot preset mismatch: blueprint 75f vs render/QC 60f — resolve which is authoritative.

## Why the loop stopped (Commander must clear)
Loop 28 changed standing config/rules that bear directly on what 'pass' means: it retired the speaker-agreement signal from the QC gate (flagged forced False), and the render uses min_shot=60f where the blueprint preset says 75f. Under the HUMAN-GATE, changes to standing config/rules and any place gate semantics were altered require Commander sign-off — especially since the authorizing prompt_028.txt was not included in the report dir to verify the retirement was ordered. The framing FAIL itself is a mechanical BLOCK, but the config changes need Commander eyes.

## Next prompt for AG  [RECOMMENDED — pending your approval]
```
BLOCK. c1 QC FAILED — not accepted. Fix, do not advance past §1.3. Raw output only, no tables.

1. qc.py dump writer: DELETE the hardcoded fallbacks (0.08 / 0.33 / 0.60 / 0.04 / 0.15). A frame with no valid detection writes null, never a target value. Paste raw `grep -n -A15 'dump_entries.append' reelcore/src/reelcore/qc.py` proving no numeric literal remains in that branch.

2. Prove the single engine: paste the raw stdout of a script that asserts framing_per_frame_dump.json == the qc.json per-frame values for every sampled frame, showing 0 mismatches. No prose.

3. Fenton CAM_A fails back-margin (1.7%<2%) and lead room (68%>67%). Derive crop H from a preset bound / formula that satisfies every framing band — do NOT paste a hand-picked H=1070 into the script. Show the formula line. Re-render, re-run QC, paste raw c1_proof.qc.json and raw QC stdout.

4. Head-size ratio must be DERIVED, not tuned to the 1.100 cap. Show the derivation of crop_h for both panels from measured face height + preset, not literals.

5. Paste raw presets/edit/magnolia_interview.yaml and presets/framing/magnolia_interview.yaml. QC min-shot uses 2.0s; blueprint says 75f (2.5s). State which is authoritative and why 60f is in the render; the 67f reaction shot is below 75f.

6. Copy prompt_028.txt into report_029/ — the agreement-retirement rule change cites it but it is absent.

Carry: cut_on_pause 66.7%, speech/room-tone 7.2 dB. STOP at the §1.3 boundary.
```