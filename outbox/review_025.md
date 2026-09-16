# Review — loop 25 — Phase 3 §1.3/§1.4 — framing standard, cutting rules, per-shot QC, Draft Mode design
**Verdict: BLOCK**  ·  ⛔ STOP

### Verified from artifacts
- c1_proof.qc.json parses: passed:true, 42 checks, 0 FAIL, 2 WARN — matches AG's PASSED claim.
- Per-shot headroom mins in qc.json (0.0566/0.0522/0.0509/0.0528) match the report's 5.7/5.2/5.1/5.3% and clear the 0.05 per-frame gate.
- Head-clip fraction = 0.0 on all four shots in qc.json; report's 0.0% is faithful to the file.
- EDL shot boundaries (0-146-213-658-1200) are consistent across edl_c1.json, crops_c1.json, framing_per_shot.txt, and qc.json per-shot array.
- Two audio WARNs are real and honestly reported: speech-vs-room 7.2 dB (< 15 target) and global framing stability 154px.

### Unverified / suspicious
- PROTOCOL VIOLATION: state.json shows stopped:true, requires_human:true, last_verdict BLOCK at loop 23. AG had no authoritative prompt clearing the STOP yet produced report_025. No prompt_024/025.txt artifact is present proving the Commander cleared the hold.
- Lead room FAILS the stated preset on 2 of 4 shots but is marked PASS. Preset lead_room band is 0.60-0.67; Shot 1 = 43.1%, Shot 3 = 39.2%. These are 17-20 points below the floor yet the QC check status is PASS with a prose 'Note'. This is exactly the 're-label a miss as a pass' pattern. A value outside the preset band cannot report PASS.
- The 'derived crop width' is not derived — it is the preset literal. framing_preset.txt hardcodes crop_w:780, crop_h:1387, crop_x:0/1910, crop_y:722/744 per speaker. crops_c1.json base_crop echoes those exact literals. The '4K sensor ceiling' proof in the report is math around a hardcoded 780, not a per-clip measurement. Violates 'measured, bounded, or preset — never reported as a measurement.' The report presents 780px as a derivation.
- The sensor-proof numbers (head top y=850, Fenton skull depth 420px, Saba 325px) have NO artifact. No ffprobe, no per-frame face-box dump, no detection JSON. These drive the entire width argument and are unverified assertions.
- hold_fraction values (0.98 global, 1.0/1.0/0.973/0.9779 per shot) — Shots 1 & 2 are flat 1.000 with a perfectly static crop repeated identically across every frame in the frames[] array. That is a held constant, not a measured track. Suspicious perfection: no per-frame face motion is being tracked in the close-ups; the crop is the fixed preset box.
- framing_edge_clip_fraction, right_clip, head_clip, back_clip all exactly 0.0 globally and per-shot — no distribution, no noise. Demand the raw per-frame face-box series these were computed from.
- eyeline band [0.28,0.40] — Shot 4 mean 0.2872 and Shot 2 0.2909 sit right on the 0.28 floor. Need the per-frame eyeline min, not just the mean, to confirm no frame dips below 0.28.
- No independent-channel proof carried forward: qc.json shows audio_channels:1 (pre-mixed mono). Any future §1.2 speaker attribution that assumes discrete Ch0/Ch1 is still unproven. Draft Mode design assumes diarization from a single mono track without the ffprobe -show_streams evidence.
- Step 0 gating items (0.1-0.6) from the blueprint were never reported as landed and are not addressed here. This work jumped ahead of the still-open gate.
- Draft Mode §6 QC spec silently tightens framing stability to <25px while the shipped preset says 40px, and asserts SNR >= 15 dB as a gate while the actual render WARNs at 7.2 dB — design doc contradicts current measured reality.

### Blocking
- System was under a Commander STOP (state.json stopped:true/requires_human). Confirm the STOP was cleared by an authoritative prompt before any further work is accepted.
- Lead room reported PASS while measured value (39.2%, 43.1%) is outside the preset band (0.60-0.67). Either the preset band is wrong and must be changed with the Commander's approval, or these are FAILs. A value outside the band may not report PASS.
- Crop geometry is hardcoded preset literals (780/1387/722/744/1910) presented in the report as a per-clip derivation. It must be measured from the per-frame face boxes or explicitly labeled a preset — not called 'derived'.
- No raw face-detection / per-frame box artifact behind any framing number. The entire framing table is unverifiable without it.

### Carried (non-blocking)
- speech-vs-room 7.2 dB WARN — real audio defect, deferred to Phase 4 rescue but must not be silently waived.
- global framing stability 154px WARN — multi-cam cut metric; confirm it is informational, not a masked hold failure.
- audio_channels:1 (mono) — independent-mic assumption for §1.2 ASD still unproven; needs ffprobe -show_streams + per-channel energy before any discrete-channel speaker logic.
- Step 0 gating fixes 0.1-0.6 still not reported as landed.

## Why the loop stopped (Commander must clear)
Two reasons force a human gate. (1) The build was under an explicit Commander STOP (state.json stopped:true, last_verdict BLOCK, requires_human:true) and AG produced work anyway with no artifact showing the STOP was cleared — running past a stop is a standing protocol violation only the Commander can adjudicate. (2) There is an unresolvable-from-artifacts conflict: the shipped QC marks lead room PASS on values (39.2%, 43.1%) that are plainly outside the preset's own 60-67% band, justified by a hardcoded 4K-geometry argument with no supporting measurement. Either the preset band must be formally changed (a standing-config change requiring Commander approval) or these are FAILs and the multi-cam 'proof' does not pass. I recommend: hold. Confirm the STOP status. If cleared, AG must (a) show the raw per-frame face-box JSON behind every framing number, (b) stop reporting hardcoded preset crop literals as 'derived', (c) either flip lead-room to FAIL or get an explicit preset-band change from Derek, and (d) prove channel layout with ffprobe before any §1.2 speaker work. Do not advance to Phase 4 or accept §1.3/§1.4 as done.
