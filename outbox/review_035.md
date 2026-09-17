# Review — loop 35 — Step 0 — Framing band calibration
**Verdict: BLOCK**  ·  ⛔ STOP

### Verified from artifacts
- Hard gates pass 8/8 shots: skull_clip_count=0 and bm_min≥0.049 for all Fenton shots, bm_min≥0.195 for all Saba shots (from solver_output_035.json)
- Locked frame confirmed: all shots show 'locked':true, 'movement':0 in JSON
- Head size ratio 1.749 calculated correctly: Fenton mean 300.2px / Saba mean 171.6px (verified arithmetic from per-shot head_px values)
- Eyeline table matches JSON: Fenton shot_2 el_med [0.364,0.400] inside [0.28,0.40], others exceed max; Saba shot_2 el_med [0.284,0.320] inside band, others dip below 0.28
- Headroom values systematically exceed [0.03,0.18] upper bound: Fenton hr_med max 0.21-0.28, Saba hr_med max 0.23-0.26 — measurement shows the old band does not describe this detector's output

### Unverified / suspicious
_(none)_

### Blocking
- Headroom band [0.03,0.18] was calibrated for skin-mask measurement; face-detector crown search reports systematically higher values (0.15-0.28 observed). The bands and the detector are now mismatched. Commander must rule on recalibrated bands before framing work can continue.

### Carried (non-blocking)
- Head size ratio 1.749 vs 1.60 ceiling — lowest-priority item per framing stack, deferred until primary bands resolved
- Step 0.1-0.6 closure status — blocked on framing band decision

## Why the loop stopped (Commander must clear)
The headroom band values are a fundamental calibration parameter. AG correctly identified that switching from skin-mask to YuNet face-detector measurement changed the scale of reported headroom values. The old band [0.03,0.18] was calibrated for the old instrument. AG proposes [0.14,0.28] based on observed data from the new instrument. Only the Commander can approve new band definitions — this is not a failure to meet targets but a measurement-basis shift requiring recalibration authority.

## Next prompt for AG  [RECOMMENDED — pending your approval]
```
RECOMMENDATION (pending Commander approval of headroom bands): If Commander accepts proposed headroom band [0.14,0.28] with mean band [0.17,0.22], update presets/framing/magnolia_interview.yaml with the new values, re-run the solver on all 8 shots, and report per-shot pass/fail against the approved bands. Include the updated preset file in the report artifacts. If Commander specifies different band values, use those instead. STOP at report boundary.
```