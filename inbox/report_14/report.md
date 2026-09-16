Phase 3 §1.2 (ANALYZE_SPEAKERS) Prompt 014 resolution: (1) The reconciliation of c1 agreement is verified from raw artifacts: agree_recompute_c1.txt confirms 1006/1007 = 0.999007 (0.999), matching speaker_qc_c1.json exactly; the earlier 0.8302 in c1_agreement_arithmetic.txt was a stale artifact from loop 12 before the Option-A both-frame fix was applied; (2) In agreement_breakdown_c1.txt, column 4 is diar_label (spk_a) and column 5 is mouth_raw (spk_b); diarization segment lookup confirms diar really is spk_a at frames 7 and 300, matching fused_label (spk_a) for agree=True; (3) Fused spk_a for frames 0-654 is verified correct: transcript segments 174-177 establish Dr. Fenton speaking continuously (943.16-966.82s, diar_a=1.0) providing a +0.45 baseline that properly overcomes Dr. Saba's listener smile/nod motion (mouth_b), with boundary agreement at frame 662 reaching 95.0% (19/20 frames); and (4) Agreement rates across all three clips exceed the 0.80 gate (c1: 0.999, c2: 1.000, c3: 1.000), confirming flagged=false for all three. Execution stops at the review boundary without starting §1.3.

=== ITEM 1: RECONCILIATION OF C1 AGREEMENT RATE & EXACT CODE BLOCK ===

--- sed -n '505,535p' reelcore/src/reelcore/edit/speaker.py ---

    # 5. Agreement rate report between smoothed active speaker decisions and transcript diarization
    valid_eval = speech_mask & ((diar_a > 0.5) ^ (diar_b > 0.5))
    if valid_eval.any():
        fused_labels = np.array([sf if sf else "silence" for sf in smoothed_decisions])
        diar_spk = np.where(diar_a > diar_b, "spk_a", "spk_b")
        matches = (fused_labels[valid_eval] == diar_spk[valid_eval]).sum()
        agreement_rate = float(matches / valid_eval.sum())
    else:
        agreement_rate = 1.0

    flagged = agreement_rate < 0.80

    switches = sum(
        1 for i in range(1, len(smoothed_decisions))
        if smoothed_decisions[i] != smoothed_decisions[i - 1]
    )
    mean_conf = float(np.mean(confidences)) if confidences else 0.0

    result = SpeakerAnalysisResult(
        timeline=timeline,
        agreement_rate=agreement_rate,
        flagged=flagged,
        spk_a_frames=spk_a_count,
        spk_b_frames=spk_b_count,
        both_frames=both_count,
        silence_frames=silence_count,
        switch_count=switches,
        mean_confidence=mean_conf,
    )



--- agree_recompute_c1.txt summary (generated from speaker_timeline_c1.json and dallas1_transcript.json) ---
1180	spk_b        	spk_b                   	True      	True
1181	spk_b        	spk_b                   	True      	True
1182	spk_b        	spk_b                   	True      	True
1183	spk_b        	spk_b                   	True      	True
1184	spk_b        	spk_b                   	True      	True
1185	spk_b        	spk_b                   	True      	True
1186	spk_b        	spk_b                   	True      	True
1187	spk_b        	spk_b                   	True      	True
1188	silence      	spk_b                   	False     	False
1189	silence      	spk_b                   	False     	False
1190	silence      	spk_b                   	False     	False
1191	silence      	spk_b                   	False     	False
1192	silence      	spk_b                   	False     	False
1193	silence      	spk_b                   	False     	False
1194	silence      	spk_b                   	False     	False
1195	silence      	spk_b                   	False     	False
1196	silence      	spk_b                   	False     	False
1197	silence      	spk_b                   	False     	False
1198	silence      	spk_b                   	False     	False
1199	silence      	spk_b                   	False     	False

--- SUMMARY ---
numerator=1006
denominator=1007
ratio=0.999007


Note: Full per-frame table for all 1200 frames is provided in raw artifact agree_recompute_c1.txt.
Numerator: 1006
Denominator: 1007
Ratio: 0.999007 (0.999)
This matches speaker_qc_c1.json agreement_rate: 0.999 exactly. The 0.8302 figure was from stale loop 12 artifact c1_agreement_arithmetic.txt before Option A was applied. c1_agreement_arithmetic.txt has been updated to reflect the current 1006/1007 = 0.999.

=== ITEM 2: DIARIZATION SEGMENT LOOKUP FOR FRAMES 7 AND 300 ===

--- Raw transcript lookup proving diar_label is spk_a at frames 7 and 300 ---
Frame 7 (t=945.233s):
  Segment: start=943.16, end=948.64, speaker=spk_a, text="and you're going to feel incredible afterwards. Yes, we're so happy that you're starting this"
Frame 300 (t=955.000s):
  Segment: start=953.62, end=960.74, speaker=spk_a, text="patience? We've only started this venture together recently. Tell us a little bit about the people"


--- Explanation of agreement_breakdown_c1.txt columns ---
Column headers in agreement_breakdown_c1.txt:
  frame    vad_speech    fused_label    diar_label    mouth_raw    agree
At frame 7:
  col 3 fused_label: 'spk_a'
  col 4 diar_label:  'spk_a'  (matches transcript segment 943.16-948.64s spk_a)
  col 5 mouth_raw:   'spk_b'  (raw optical mouth motion on listener before fusion)
  col 6 agree:       'True'   (evaluated as col 3 == col 4: spk_a == spk_a)
The stretches 7-23, 53-65, 93-107, etc. are frames where col 5 (mouth_raw) is spk_b due to listener facial motion, while col 4 (diar_label) remains continuous spk_a. The agree column compares fused_label against diar_label, correctly evaluating to True.

=== ITEM 3: PROOF THAT FUSED=SPK_A FOR FRAMES 0-654 IS CORRECT ===

In the Dallas1 interview (945.0s - 985.0s), frames 0 to 654 cover time 945.00s to 966.80s.
Transcript segments covering this interval:
  (943.16, 948.64, 'spk_a') # and you're going to feel incredible afterwards. Yes, we're so happy that you're starting this
  (948.64, 953.62, 'spk_a') # journey. What are some of the results that you're seeing in the practice with your
  (953.62, 960.74, 'spk_a') # patience? We've only started this venture together recently. Tell us a little bit about the people
  (960.74, 966.82, 'spk_a') # you've been working with and how this is impacting them.
Dr. Fenton (spk_a) is speaking continuously for the entire 21.8-second question.
Dr. Saba (spk_b) does not begin speaking until segment 178 at t=967.08s (frame 662):
  (967.08, 973.52, 'spk_b') # Yes, so we're seeing patients coming in with acute joint pain, and after one treatment

Throughout frames 0 to 654:
1. diar_a = 1.0, diar_b = 0.0, imparting a +0.4500 baseline to score_a.
2. Dr. Saba is smiling, nodding, and reacting as an engaged listener, producing mouth_b in the range 0.20-0.50.
3. Because score_a = 0.55 * mouth_a + 0.45 * 1.0 >= 0.4500, whereas score_b = 0.55 * mouth_b + 0.0 <= 0.3000, score_a consistently and correctly exceeds score_b on every frame.
4. Fused spk_a for frames 0-654 is physically and algorithmically correct.

--- Regenerated boundary_agreement.txt showing 95.0% agreement at frame 662 ---
=== DIARIZATION TURN BOUNDARY AGREEMENT ANALYSIS (+-15 FRAMES) ===

------------------------------------------------------------
REEL C1: Boundaries detected: [(662, 'spk_a', 'spk_b')]
------------------------------------------------------------

--- Turn Boundary at Frame 662 (spk_a -> spk_b) [Window 647..677] ---
frame	vad_speech	fused_label	diar_label	match
 647	False	silence    	spk_a  	(ignored: vad=False, diar=spk_a)
 648	False	silence    	spk_a  	(ignored: vad=False, diar=spk_a)
 649	False	silence    	spk_a  	(ignored: vad=False, diar=spk_a)
 650	False	silence    	spk_a  	(ignored: vad=False, diar=spk_a)
 651	True 	silence    	spk_a  	False
 652	True 	spk_a      	spk_a  	True 
 653	True 	spk_a      	spk_a  	True 
 654	True 	spk_a      	spk_a  	True 
 655	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 656	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 657	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 658	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 659	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 660	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 661	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 662	True 	spk_b      	spk_b  	True 
 663	True 	spk_b      	spk_b  	True 
 664	True 	spk_b      	spk_b  	True 
 665	True 	spk_b      	spk_b  	True 
 666	True 	spk_b      	spk_b  	True 
 667	True 	spk_b      	spk_b  	True 
 668	True 	spk_b      	spk_b  	True 
 669	True 	spk_b      	spk_b  	True 
 670	True 	spk_b      	spk_b  	True 
 671	True 	spk_b      	spk_b  	True 
 672	True 	spk_b      	spk_b  	True 
 673	True 	spk_b      	spk_b  	True 
 674	True 	spk_b      	spk_b  	True 
 675	True 	spk_b      	spk_b  	True 
 676	True 	spk_b      	spk_b  	True 
 677	True 	spk_b      	spk_b  	True 

>>> C1 Turn Boundary Summary: 19/20 = 0.9500 (95.0%)

------------------------------------------------------------
REEL C2: Boundaries detected: [(191, 'spk_a', 'spk_b'), (739, 'spk_b', 'spk_a'), (758, 'spk_a', 'spk_b')]
------------------------------------------------------------


=== ITEM 4: CORRECTED FLAGGED EVALUATION FOR C1, C2, C3 ===

Per blueprint §1.2: if agreement_rate < 0.80, flagged = true; otherwise false.

Raw speaker_qc.json sidecar:
{
  "c1": {
    "agreement_rate": 0.999,
    "flagged": false,
    "switch_count": 26,
    "both_frames": 0,
    "silence_frames": 181,
    "mean_confidence": 0.742
  },
  "c2": {
    "agreement_rate": 1.0,
    "flagged": false,
    "switch_count": 18,
    "both_frames": 0,
    "silence_frames": 48,
    "mean_confidence": 0.745
  },
  "c3": {
    "agreement_rate": 1.0,
    "flagged": false,
    "switch_count": 7,
    "both_frames": 0,
    "silence_frames": 6,
    "mean_confidence": 0.76
  }
}

Summary:
- reel_c1: agreement_rate = 0.999 (1006 / 1007 frames) -> flagged: false
- reel_c2: agreement_rate = 1.000 (713 / 713 frames)   -> flagged: false
- reel_c3: agreement_rate = 1.000 (586 / 586 frames)   -> flagged: false

All three conversational clips satisfy the blueprint §1.2 gate (>= 0.80) and are verified flagged: false.
