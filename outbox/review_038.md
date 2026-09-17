# Review — loop 38 — Dallas Closure + Florida Ingest
**Verdict: REVISE**

### Verified from artifacts
- crown_fallback_ratio exists in preset at line 25 (dallas_preset_grep.txt)
- face_framing.py:260 is a function parameter default, not a computation literal
- 0.03 hits in solver scripts are diagnostic archives, not active render path
- Florida video stream metadata captured (florida_ffprobe.txt)

### Unverified / suspicious
- face_framing.py:304 usage claim — no code snippet showing what line 304 actually uses
- Florida compare file slice origin — no artifact showing slice extraction command or original file duration
- Florida video presence — source_tiers_tail.txt says 'NOT YET OFFLOADED' but ffprobe shows video data; discrepancy not addressed
- -0.027 correlation for Florida audio alignment — suspiciously weak, no investigation

### Blocking
- Florida video status discrepancy must be clarified before proceeding
- Florida audio alignment remains unverified

### Carried (non-blocking)
- Dallas is frozen per Ruling 036 — no further optimization
- Florida is default tier (full professional standard, no exceptions)

## Next prompt for AG  [SENT to AG]
```
Loop 038 REVISE. Three items required:

1. **face_framing.py line 304**: Paste lines 300-310 from `reelcore/src/reelcore/face_framing.py` showing the variable `crown_fallback_ratio` is used (not a literal). One artifact: `face_framing_304.txt`.

2. **Florida video status**: The source_tiers doc says video NOT YET OFFLOADED as of 2026-09-17, but you provided ffprobe output. Clarify: (a) What file path did ffprobe run on? (b) Was video offloaded today? If yes, update source_tiers doc. Artifact: full ffprobe command with path.

3. **Florida audio alignment**: The -0.027 correlation is too weak to trust. Either (a) verify the slice extraction by showing the ffmpeg command that created `*_MIC_TX03.wav` and the original file duration, OR (b) re-slice from the original using the correct offset and re-run correlation. Artifact: slice verification or re-slice + new correlation result.

Raw output only. No tables. Stop at the next boundary.
```