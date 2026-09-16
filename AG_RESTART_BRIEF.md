# AG RESTART BRIEF — read this first in a fresh window

You are Antigravity. Your previous window ran out of context mid-loop-030.
Nothing is lost — all work is committed to git (7b8e639) and pushed.

## READ THESE FIRST, IN ORDER
1. AGENTS.md — especially ROLE LOCK, CLOSED PREMISES, SELF-APPROVAL BAN,
   HARD STOP RULE, HANDOFF PROTOCOL.
2. handoff/outbox/prompt_030.txt — your current directive. It is authoritative.
3. handoff/outbox/review_029.md — the last audit.

## ROLES (do not invert these)
- You BUILD. You are not the supervisor and you do not author Commander Rulings.
- The reviewer (tools/handoff/bridge.py) AUDITS. It is not the builder.
- Derek COMMANDS. Only Derek clears a STOP.

## WHERE LOOP 030 STANDS — DO NOT REDO THIS WORK
Already done and committed:
- reelcore/src/reelcore/qc.py modified for the locked-framing / sustained
  measurement work.
- output/PHASE_3_CONVERSATIONAL/solved_locked_crops.json PRODUCED. The static
  crop solver RAN and it WORKS. Sample results:
    fenton shot_1: crop [0,770,1339,1190], in_band 0.96, med_in_band true,
                   max_exc_frames 6, head 294.7px, bm_min 0.028
    fenton shot_2: crop [0,760,1354,1204], in_band 1.00, med_in_band true,
                   max_exc_frames 0, head 298.5px, bm_min 0.060
  Read that file before doing anything — the solve is finished.

## WHAT REMAINS FOR LOOP 030
1. Read solved_locked_crops.json and apply those static crops to the render.
   Each shot uses ONE static crop box. movement = 0. No per-frame tracking.
2. Re-render output/PHASE_3_CONVERSATIONAL/c1_proof.mp4 with the locked crops.
3. Run QC with the sustained (rolling-median) + bounded-transient measurement
   described in prompt_030.txt.
4. Write locked_framing.txt reporting, per panel per shot: the static crop box
   and height, whether it LOCKED (movement 0), eyeline/headroom rolling-median
   min/mean/max, instantaneous max, longest excursion in frames, total fraction
   outside band, head size px and resulting ratio, and what head size was given
   up to achieve the lock.
5. WATCH THIS: solved lead-room means are ~0.669, right at the 0.67 band
   ceiling. Report the measured value honestly. If it breaches, say so — do not
   force a pass and do not widen the band yourself; propose it with numbers.
6. Assemble handoff/inbox/report_030/ with report.md (one short paragraph, no
   tables) plus every raw artifact you cite. A cited-but-missing file is an
   automatic REVISE. Include solved_locked_crops.json and locked_framing.txt.
7. touch handoff/inbox/report_030/DONE LAST.
8. Run: ./venv/bin/python3 tools/handoff/bridge.py --once
   If it fails, paste the error verbatim and STOP. Never substitute your own
   summary for a review.
9. Push the handoff mirror: bash tools/handoff/sync_github.sh
10. STOP.

## CONTEXT HYGIENE (so this does not happen again)
- Do not re-read large artifacts you have already processed.
- Do not run exploratory sweeps that are already solved — the crop solve is done.
- Commit early and often: git add -A && git commit -m "..." && git push
- If you feel context filling, write your state into
  handoff/AG_RESTART_BRIEF.md (this file), commit, and say so plainly.

## REPO / BACKUP
- Code: github.com/suasponte1313/magnolia-video-engine (private, origin)
- Handoff mirror: github.com/suasponte1313/magnolia-handoff (public)
