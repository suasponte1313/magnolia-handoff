# Review — loop 31 — Phase 3 §1 — locked framing solver
**Verdict: REVISE**  ·  ⛔ STOP

### Verified from artifacts
- framing_priority_order added to preset with correct stack (hard_gates > locked_frame > soft_bands > head_size)
- head_size_ratio_max raised to 1.45 in preset
- max_crop_height_for_lock = 1400 in preset
- Fenton all 4 shots pass headroom band [5%, 12%] — solver_output and locked_framing agree
- Saba Shot 2 passes headroom band in both artifacts
- Hard gates (zero skull clip, back margin) pass all shots/panels
- Locked frame achieved (movement = 0px) in all shots
- Saba vertical posture swing causes sustained out-of-band headroom in shots 1, 3, 4 — failure is real, not a placeholder

### Unverified / suspicious
- Artifact discrepancy: solver_output.txt says Saba Shot 2 ch=1360, locked_framing.txt shows ch=1200 — which is correct?
- Artifact discrepancy: solver_output.txt Saba Shot 3 ch=1200, locked_framing.txt ch=1120
- Solver exited with code 144 before completing Shot 4 for Saba — why? Was the run incomplete?
- Minor number discrepancy: report.md says Shot 1 min 4.0%, locked_framing says 4.3%
- Shot 4 head_size_ratio 1.547x exceeds new 1.45x limit — preset violation persists

### Blocking
- Explain artifact discrepancy between solver_output.txt and locked_framing.txt crop heights
- Explain solver exit code 144 — complete the run or explain why it cannot complete

### Carried (non-blocking)
- No single excursion > 45f (reposition_after_frames), but total_outside_band > 10% in shots 3/4 — rules create unsatisfiable constraint
- Shot 4 ratio 1.547x exceeds head_size_ratio_max=1.45 even after raising the limit

## Why the loop stopped (Commander must clear)
Solver demonstrates mutually unsatisfiable constraints: (1) no excursion > 45f so reposition forbidden, (2) total_outside_band > 10% so soft_bands fail, (3) head_size already sacrificed to 1.547x exceeding 1.45 limit. Commander must rule which constraint relaxes: allow reposition on cumulative out-of-band, widen headroom band beyond [5%, 12%], or accept headroom violations for this source.

## Next prompt for AG  [RECOMMENDED — pending your approval]
```
REVISE before Commander ruling. Two artifacts disagree:

1. solver_output.txt says Saba Shot 2 passes at ch=1360. locked_framing.txt says Shot 2 crop height is 1200px. Which is the actual solution applied?

2. solver_output.txt exited with code 144 before completing Saba Shot 4. Why? Was the locked_framing.txt generated from a different/complete run?

Re-run the solver to completion OR explain the artifact provenance. Paste the raw stderr if the solver crashes. Do not proceed to reposition logic until these discrepancies are resolved.

STOP after providing the clarification.
```