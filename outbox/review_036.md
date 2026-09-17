# Review — loop 36 — Crown measurement verification per Ruling 036
**Verdict: BLOCK**  ·  ⛔ STOP

### Verified from artifacts
- Hard gates pass 8/8: skull_clip_count=0, bm_violations=0 for all shots (solver_output_036.json)
- Crown line placement with 0.03 fallback is visually accurate - PNGs show cyan line at actual hair crown, not floating above
- Headroom genuinely measures 0.18-0.28 across all shots (solver_output_036.json: hr_med ranges confirmed)
- Eyeline ranges [0.26-0.46] for Saba, [0.33-0.46] for Fenton from solver_output_036.json
- Math on re-solve: 0.22-0.08=0.14 headroom reduction * 1070px = ~150px shift would push eyeline from 0.37 to 0.51, outside [0.28, 0.40]
- 6 diagnostic PNGs present and readable, 1 skin mask region PNG present
- Detection stats show fallback_0.03 dominates crown detection (no suspicious perfection)

### Unverified / suspicious
- 0.03 fallback ratio is hardcoded at face_framing.py:304, not loaded from preset - violates 'measured, bounded, or preset — never a global constant'
- AG did not attempt actual re-solve with new crops - only computed on paper that it is 'infeasible'
- 'Infeasible' is AG's judgment call, not a demonstrated impossibility - Commander should see actual numbers from attempted re-solve

### Blocking
- Decision to freeze Dallas at current crops and move tuning to Florida is strategic — requires Commander approval, not reviewer signoff
- Ruling 3(a) ordered: 'Re-solve the locked crops to bring measured headroom to the professional target' — AG computed the cost but did not present a re-solve attempt for Commander to accept or reject

### Carried (non-blocking)
- 0.03 crown fallback literal should be moved to preset for the next source (minor, not blocking for Dallas freeze)
- Florida 2026-09-13 shoot ready for engine tuning once Dallas is frozen

## Why the loop stopped (Commander must clear)
AG verified crown measurement is now accurate. Headroom is genuinely 18-28% vs 5-12% target. Re-solving would break eyeline band. This is a real constraint conflict the Commander must resolve: (a) accept current crops with excessive headroom, (b) re-solve accepting eyeline violation, or (c) freeze Dallas as degraded per Ruling 4. AG recommends (c) but made the call themselves instead of presenting options.

## Next prompt for AG  [RECOMMENDED — pending your approval]
```
COMMANDER OPTIONS for loop 036 constraint conflict:

FINDINGS VERIFIED:
- Crown measurement now accurate (0.03 fallback, diagnostic PNGs confirm)
- Headroom genuinely 18-28% (professional target 5-12%)
- Re-solve math: ~150px vertical shift needed, would push eyeline to ~0.51 (band is [0.28, 0.40])
- Hard gates pass 8/8, head size ratio 1.749x (already waived)

OPTION A: Freeze Dallas c1 at current crops (excessive headroom, eyeline in band)
- Aligns with Ruling 4: Dallas is degraded, move tuning to Florida
- Accept 18-28% headroom as source limitation

OPTION B: Re-solve for professional headroom (0.08), accept eyeline violation
- Headroom becomes professional-grade
- Eyeline ~0.51 exceeds [0.28, 0.40] band

OPTION C: Attempt compromise crops (partial headroom improvement, partial eyeline sacrifice)
- AG did not explore this - could try ~75px shift targeting 0.15 headroom, eyeline ~0.44

Please select an option or provide alternative direction. If Option A: confirm Dallas c1 frozen, next prompt directs AG to begin Florida shoot analysis under full standard.
```