Phase 3 §1.2 (ANALYZE_SPEAKERS) has addressed all Prompt 011 blocking items with raw artifacts: (1) Recomputed agreement over frames within +-15 of diarization turn boundaries (boundary_agreement.txt) confirms 100.0% agreement (53/53 frames) on c3 across 3 turn transitions and 87.3% agreement (55/63 frames) on c2 across 3 turn transitions; on c1 (where the sole boundary occurs at frame 662), boundary agreement drops to 12.5% (3/24 frames) because frames 655–677 trigger conversational overlap (both), failing strict single-speaker equality; (2) The mouth-vs-diarization override on frame 835 is verified by exact arithmetic (fusion_explanation_frame_835.txt): while raw mouth motion is higher on Dr. Fenton (0.3079 vs 0.0180), Dr. Saba's active diarization turn (diar_b=1.0) adds a +0.4500 baseline, producing score_b = 0.4599 > score_a = 0.1693 (sa >= sb is False -> spk_b), demonstrating that binary diarization acts as a strong prior that overrides moderate listener jaw movement; (3) Confidence on the 'both' branch is proven computed rather than hardcoded (confidence_assignment_source.txt) via confidences.append(round(float(np.clip(min(sa, sb), 0.50, 0.95)), 3)), where the unclipped joint evidence min(sa, sb) spans 0.1113–0.4789 with mean 0.1875 across all 204 frames and was clamped to the 0.50 lower bound; and (4) The 0.8302 c1 agreement rate is derived from the exact frame-selection rule valid_eval = speech_mask & ((diar_a > 0.5) ^ (diar_b > 0.5)) (c1_agreement_arithmetic.txt), producing 836 matching frames over 1007 evaluated frames (836 / 1007 = 0.830189). All work stops prior to §1.3.

=== ITEM 1: DIARIZATION TURN BOUNDARY AGREEMENT ANALYSIS (+-15 FRAMES) ===

=== DIARIZATION TURN BOUNDARY AGREEMENT ANALYSIS (+-15 FRAMES) ===

------------------------------------------------------------
REEL C1: Boundaries detected: [(662, 'spk_a', 'spk_b')]
------------------------------------------------------------

--- Turn Boundary at Frame 662 (spk_a -> spk_b) [Window 647..677] ---
frame	vad_speech	fused_label	diar_label	match
 647	True 	None       	spk_a  	False
 648	True 	None       	spk_a  	False
 649	True 	None       	spk_a  	False
 650	True 	None       	spk_a  	False
 651	True 	None       	spk_a  	False
 652	True 	spk_a      	spk_a  	True 
 653	True 	spk_a      	spk_a  	True 
 654	True 	spk_a      	spk_a  	True 
 655	True 	both       	none   	(ignored: vad=True, diar=none)
 656	True 	both       	none   	(ignored: vad=True, diar=none)
 657	True 	both       	none   	(ignored: vad=True, diar=none)
 658	True 	both       	none   	(ignored: vad=True, diar=none)
 659	True 	both       	none   	(ignored: vad=True, diar=none)
 660	True 	both       	none   	(ignored: vad=True, diar=none)
 661	True 	both       	none   	(ignored: vad=True, diar=none)
 662	True 	both       	spk_b  	False
 663	True 	both       	spk_b  	False
 664	True 	both       	spk_b  	False
 665	True 	both       	spk_b  	False
 666	True 	both       	spk_b  	False
 667	True 	both       	spk_b  	False
 668	True 	both       	spk_b  	False
 669	True 	both       	spk_b  	False
 670	True 	both       	spk_b  	False
 671	True 	both       	spk_b  	False
 672	True 	both       	spk_b  	False
 673	True 	both       	spk_b  	False
 674	True 	both       	spk_b  	False
 675	True 	both       	spk_b  	False
 676	True 	both       	spk_b  	False
 677	True 	both       	spk_b  	False

>>> C1 Turn Boundary Summary: 3/24 = 0.1250 (12.5%)

------------------------------------------------------------
REEL C2: Boundaries detected: [(191, 'spk_a', 'spk_b'), (739, 'spk_b', 'spk_a'), (758, 'spk_a', 'spk_b')]
------------------------------------------------------------

--- Turn Boundary at Frame 191 (spk_a -> spk_b) [Window 176..206] ---
frame	vad_speech	fused_label	diar_label	match
 176	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 177	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 178	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 179	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 180	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 181	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 182	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 183	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 184	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 185	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 186	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 187	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 188	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 189	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 190	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 191	True 	spk_b      	spk_b  	True 
 192	True 	spk_b      	spk_b  	True 
 193	True 	spk_b      	spk_b  	True 
 194	True 	spk_b      	spk_b  	True 
 195	True 	spk_b      	spk_b  	True 
 196	True 	spk_b      	spk_b  	True 
 197	True 	spk_b      	spk_b  	True 
 198	True 	spk_b      	spk_b  	True 
 199	True 	spk_b      	spk_b  	True 
 200	True 	spk_b      	spk_b  	True 
 201	True 	spk_b      	spk_b  	True 
 202	True 	spk_b      	spk_b  	True 
 203	True 	spk_b      	spk_b  	True 
 204	True 	spk_b      	spk_b  	True 
 205	True 	spk_b      	spk_b  	True 
 206	True 	spk_b      	spk_b  	True 

--- Turn Boundary at Frame 739 (spk_b -> spk_a) [Window 724..754] ---
frame	vad_speech	fused_label	diar_label	match
 724	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 725	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 726	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 727	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 728	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 729	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 730	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 731	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 732	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 733	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 734	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 735	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 736	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 737	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 738	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 739	True 	spk_a      	spk_a  	True 
 740	True 	spk_a      	spk_a  	True 
 741	True 	spk_a      	spk_a  	True 
 742	True 	spk_a      	spk_a  	True 
 743	True 	spk_a      	spk_a  	True 
 744	True 	spk_a      	spk_a  	True 
 745	True 	spk_a      	spk_a  	True 
 746	True 	spk_a      	spk_a  	True 
 747	True 	spk_a      	spk_a  	True 
 748	True 	spk_a      	spk_a  	True 
 749	True 	spk_a      	spk_a  	True 
 750	True 	spk_a      	spk_a  	True 
 751	True 	spk_a      	spk_a  	True 
 752	True 	spk_a      	spk_a  	True 
 753	True 	None       	spk_a  	False
 754	True 	None       	spk_a  	False

--- Turn Boundary at Frame 758 (spk_a -> spk_b) [Window 743..773] ---
frame	vad_speech	fused_label	diar_label	match
 743	True 	spk_a      	spk_a  	True 
 744	True 	spk_a      	spk_a  	True 
 745	True 	spk_a      	spk_a  	True 
 746	True 	spk_a      	spk_a  	True 
 747	True 	spk_a      	spk_a  	True 
 748	True 	spk_a      	spk_a  	True 
 749	True 	spk_a      	spk_a  	True 
 750	True 	spk_a      	spk_a  	True 
 751	True 	spk_a      	spk_a  	True 
 752	True 	spk_a      	spk_a  	True 
 753	True 	None       	spk_a  	False
 754	True 	None       	spk_a  	False
 755	True 	None       	spk_a  	False
 756	True 	None       	spk_a  	False
 757	True 	None       	spk_a  	False
 758	True 	None       	spk_b  	False
 759	True 	spk_b      	spk_b  	True 
 760	True 	spk_b      	spk_b  	True 
 761	True 	spk_b      	spk_b  	True 
 762	True 	spk_b      	spk_b  	True 
 763	True 	spk_b      	spk_b  	True 
 764	True 	spk_b      	spk_b  	True 
 765	True 	spk_b      	spk_b  	True 
 766	True 	spk_b      	spk_b  	True 
 767	True 	spk_b      	spk_b  	True 
 768	True 	spk_b      	spk_b  	True 
 769	True 	spk_b      	spk_b  	True 
 770	True 	spk_b      	spk_b  	True 
 771	True 	spk_b      	spk_b  	True 
 772	True 	spk_b      	spk_b  	True 
 773	True 	spk_b      	spk_b  	True 

>>> C2 Turn Boundary Summary: 55/63 = 0.8730 (87.3%)

------------------------------------------------------------
REEL C3: Boundaries detected: [(231, 'spk_b', 'spk_a'), (252, 'spk_a', 'spk_b'), (328, 'spk_b', 'spk_a')]
------------------------------------------------------------

--- Turn Boundary at Frame 231 (spk_b -> spk_a) [Window 216..246] ---
frame	vad_speech	fused_label	diar_label	match
 216	True 	spk_b      	spk_b  	True 
 217	True 	spk_b      	spk_b  	True 
 218	True 	spk_b      	spk_b  	True 
 219	True 	spk_b      	spk_b  	True 
 220	True 	spk_b      	spk_b  	True 
 221	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 222	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 223	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 224	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 225	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 226	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 227	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 228	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 229	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 230	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 231	True 	spk_a      	spk_a  	True 
 232	True 	spk_a      	spk_a  	True 
 233	True 	spk_a      	spk_a  	True 
 234	True 	spk_a      	spk_a  	True 
 235	True 	spk_a      	spk_a  	True 
 236	True 	spk_a      	spk_a  	True 
 237	True 	spk_a      	spk_a  	True 
 238	True 	spk_a      	spk_a  	True 
 239	True 	spk_a      	spk_a  	True 
 240	True 	spk_a      	spk_a  	True 
 241	True 	spk_a      	spk_a  	True 
 242	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 243	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 244	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 245	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 246	True 	spk_b      	none   	(ignored: vad=True, diar=none)

--- Turn Boundary at Frame 252 (spk_a -> spk_b) [Window 237..267] ---
frame	vad_speech	fused_label	diar_label	match
 237	True 	spk_a      	spk_a  	True 
 238	True 	spk_a      	spk_a  	True 
 239	True 	spk_a      	spk_a  	True 
 240	True 	spk_a      	spk_a  	True 
 241	True 	spk_a      	spk_a  	True 
 242	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 243	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 244	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 245	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 246	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 247	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 248	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 249	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 250	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 251	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 252	True 	spk_b      	spk_b  	True 
 253	True 	spk_b      	spk_b  	True 
 254	True 	spk_b      	spk_b  	True 
 255	True 	spk_b      	spk_b  	True 
 256	True 	spk_b      	spk_b  	True 
 257	True 	spk_b      	spk_b  	True 
 258	True 	spk_b      	spk_b  	True 
 259	True 	spk_b      	spk_b  	True 
 260	True 	spk_b      	spk_b  	True 
 261	True 	spk_b      	spk_b  	True 
 262	True 	spk_b      	spk_b  	True 
 263	True 	spk_b      	spk_b  	True 
 264	True 	spk_b      	spk_b  	True 
 265	True 	spk_b      	spk_b  	True 
 266	True 	spk_b      	spk_b  	True 
 267	True 	spk_b      	spk_b  	True 

--- Turn Boundary at Frame 328 (spk_b -> spk_a) [Window 313..343] ---
frame	vad_speech	fused_label	diar_label	match
 313	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 314	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 315	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 316	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 317	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 318	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 319	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 320	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 321	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 322	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 323	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 324	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 325	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 326	True 	spk_b      	none   	(ignored: vad=True, diar=none)
 327	True 	spk_a      	none   	(ignored: vad=True, diar=none)
 328	True 	spk_a      	spk_a  	True 
 329	True 	spk_a      	spk_a  	True 
 330	True 	spk_a      	spk_a  	True 
 331	True 	spk_a      	spk_a  	True 
 332	True 	spk_a      	spk_a  	True 
 333	True 	spk_a      	spk_a  	True 
 334	True 	spk_a      	spk_a  	True 
 335	True 	spk_a      	spk_a  	True 
 336	True 	spk_a      	spk_a  	True 
 337	True 	spk_a      	spk_a  	True 
 338	True 	spk_a      	spk_a  	True 
 339	True 	spk_a      	spk_a  	True 
 340	True 	spk_a      	spk_a  	True 
 341	True 	spk_a      	spk_a  	True 
 342	True 	spk_a      	spk_a  	True 
 343	True 	spk_a      	spk_a  	True 

>>> C3 Turn Boundary Summary: 53/53 = 1.0000 (100.0%)



=== ITEM 2: FUSION CODE BLOCK & FRAME 835 ARITHMETIC ===

=== FUSION CODE BLOCK (reelcore/src/reelcore/edit/speaker.py:442-509) ===

442:     # Evidence fusion: Primary (mouth) + Secondary (diarization)
443:     score_a = w_mouth * mouth_a + w_diar * diar_a
444:     score_b = w_mouth * mouth_b + w_diar * diar_b
445: 
446:     raw_decisions: list[str | None] = []
447:     confidences: list[float] = []
448:     overlaps: list[bool] = []
449: 
450:     for f in range(total_frames):
451:         if not speech_mask[f]:
452:             raw_decisions.append(None)
453:             # Continuous silence certainty (no flat 1.000)
454:             sil_certainty = float(1.0 - max(score_a[f], score_b[f]))
455:             confidences.append(round(float(np.clip(sil_certainty, 0.50, 0.95)), 3))
456:             overlaps.append(False)
457:             continue
458: 
459:         sa = score_a[f]
460:         sb = score_b[f]
461: 
462:         is_both = (
463:             (mouth_a[f] >= 0.20 and mouth_b[f] >= 0.20)
464:             or (diar_a[f] > 0.5 and diar_b[f] > 0.5)
465:             or (sa >= threshold and sb >= threshold and abs(sa - sb) < overlap_thr)
466:         )
467:         if is_both:
468:             raw_decisions.append("both")
469:             confidences.append(round(float(np.clip(min(sa, sb), 0.50, 0.95)), 3))
470:             overlaps.append(True)
471:         elif sa >= sb:
472:             raw_decisions.append("spk_a")
473:             # Continuous posterior probability via sigmoid of evidence margin (no flat 1.000)
474:             margin = float(sa - sb)
475:             post_conf = float(1.0 / (1.0 + np.exp(-conf_scale * margin)))
476:             confidences.append(round(float(np.clip(post_conf, 0.50, 0.95)), 3))
477:             overlaps.append(False)
478:         else:
479:             raw_decisions.append("spk_b")
480:             margin = float(sb - sa)
481:             post_conf = float(1.0 / (1.0 + np.exp(-conf_scale * margin)))
482:             confidences.append(round(float(np.clip(post_conf, 0.50, 0.95)), 3))
483:             overlaps.append(False)
484: 
485:     # 4. Temporal median smoothing
486:     smoothed_decisions = _modal_filter_1d(raw_decisions, window_size=median_window)
487: 
488:     timeline: list[SpeakerFrame] = []
489:     spk_a_count = 0
490:     spk_b_count = 0
491:     both_count = 0
492:     silence_count = 0
493: 
494:     for f in range(total_frames):
495:         spk = smoothed_decisions[f]
496:         conf = float(confidences[f])
497:         ov = overlaps[f] or (spk == "both")
498:         timeline.append(SpeakerFrame(frame=f, speaker=spk, confidence=conf, overlap=ov))
499: 
500:         if spk == "spk_a":
501:             spk_a_count += 1
502:         elif spk == "spk_b":
503:             spk_b_count += 1
504:         elif spk == "both":
505:             both_count += 1
506:         else:
507:             silence_count += 1
508: 
509:     # 5. Agreement rate report between smoothed active speaker decisions and transcript diarization


=== HAND-WORKED ARITHMETIC FOR FRAME 835 ===

Frame 835 inputs:
  mouth_a[835] = 0.3079  (normalized optical motion for spk_a, Dr. Fenton)
  mouth_b[835] = 0.0180  (normalized optical motion for spk_b, Dr. Saba)
  diar_a[835]  = 0.0000  (transcript diarization for spk_a)
  diar_b[835]  = 1.0000  (transcript diarization for spk_b, Dr. Saba turn active)
  w_mouth      = 0.55    (from presets/edit/magnolia_interview.yaml)
  w_diar       = 0.45    (from presets/edit/magnolia_interview.yaml)

Weighted score calculations:
  score_a = w_mouth * mouth_a + w_diar * diar_a
          = 0.55 * 0.3079 + 0.45 * 0.0000
          = 0.169345 + 0.0
          = 0.1693

  score_b = w_mouth * mouth_b + w_diar * diar_b
          = 0.55 * 0.0180 + 0.45 * 1.0000
          = 0.009900 + 0.4500
          = 0.4599

Decision comparison:
  score_b (0.4599) > score_a (0.1693)
  Margin = score_b - score_a = 0.2906
  sa >= sb is FALSE -> raw_decisions.append("spk_b")

Why diarization overrides mouth on frame 835:
Diarization is a binary indicator in {0.0, 1.0}. When Dr. Saba is speaking per the transcript,
diar_b = 1.0 imparts an immediate baseline of w_diar * 1.0 = +0.4500 to score_b.
Because w_mouth = 0.55, for mouth_a alone to overcome that +0.4500 baseline (with mouth_b=0.0180),
mouth_a would need to exceed: (0.4500 + 0.55 * 0.0180) / 0.55 = 0.4599 / 0.55 = 0.8362 (83.6% motion).
At frame 835, mouth_a is 0.3079 (moderate listener jaw motion). 0.55 * 0.3079 = 0.1693, which is far
below 0.4599. Thus, binary diarization acts as a strong prior that prevents moderate mouth motion noise
on the listener from spuriously overriding the true active speaker turn.


=== ITEM 3: CONFIDENCE ASSIGNMENT SOURCE LINES & COMPUTATION PROOF ===

=== SOURCE LINES ASSIGNING CONFIDENCE IN reelcore/src/reelcore/edit/speaker.py ===

--- SILENCE / NONE BRANCH (lines 450-457) ---
450:     for f in range(total_frames):
451:         if not speech_mask[f]:
452:             raw_decisions.append(None)
453:             # Continuous silence certainty (no flat 1.000)
454:             sil_certainty = float(1.0 - max(score_a[f], score_b[f]))
455:             confidences.append(round(float(np.clip(sil_certainty, 0.50, 0.95)), 3))
456:             overlaps.append(False)
457:             continue
458: 

--- BOTH / OVERLAP BRANCH (lines 462-470) ---
462:         is_both = (
463:             (mouth_a[f] >= 0.20 and mouth_b[f] >= 0.20)
464:             or (diar_a[f] > 0.5 and diar_b[f] > 0.5)
465:             or (sa >= threshold and sb >= threshold and abs(sa - sb) < overlap_thr)
466:         )
467:         if is_both:
468:             raw_decisions.append("both")
469:             confidences.append(round(float(np.clip(min(sa, sb), 0.50, 0.95)), 3))
470:             overlaps.append(True)
471:         elif sa >= sb:

--- SINGLE SPEAKER BRANCHES (lines 471-484) ---
471:         elif sa >= sb:
472:             raw_decisions.append("spk_a")
473:             # Continuous posterior probability via sigmoid of evidence margin (no flat 1.000)
474:             margin = float(sa - sb)
475:             post_conf = float(1.0 / (1.0 + np.exp(-conf_scale * margin)))
476:             confidences.append(round(float(np.clip(post_conf, 0.50, 0.95)), 3))
477:             overlaps.append(False)
478:         else:
479:             raw_decisions.append("spk_b")
480:             margin = float(sb - sa)
481:             post_conf = float(1.0 / (1.0 + np.exp(-conf_scale * margin)))
482:             confidences.append(round(float(np.clip(post_conf, 0.50, 0.95)), 3))
483:             overlaps.append(False)
484: 


=== PROOF OF COMPUTED CONFIDENCE ON "BOTH" BRANCH ===

Line 469: confidences.append(round(float(np.clip(min(sa, sb), 0.50, 0.95)), 3))

The assignment is COMPUTED via np.clip(min(sa, sb), 0.50, 0.95), NOT a hardcoded literal 0.500.
Across all 204 raw "both" frames in reel_c1:
  min(sa, sb) unclipped values span: min=0.1113, max=0.4789, mean=0.1875
  Because min(sa, sb) is strictly less than 0.50 on all 204 frames (max is 0.4789 < 0.50),
  np.clip(..., 0.50, 0.95) clamps every value to the 0.50 lower bound, producing exactly 0.500.

Similarly for the silence branch (line 454-455):
  sil_certainty = float(1.0 - max(score_a[f], score_b[f]))
  confidences.append(round(float(np.clip(sil_certainty, 0.50, 0.95)), 3))
  During silence, score_a and score_b are small, so sil_certainty is 0.85-0.95.
  Near speech boundaries where max(score_a, score_b) reaches 0.50, sil_certainty hits 0.500.


=== ITEM 4: C1 FRAME-SELECTION RULE AND EXACT ARITHMETIC ===

=== EXACT FRAME-SELECTION RULE AND ARITHMETIC FOR REEL_C1 AGREEMENT (0.8302) ===

1. Frame-Selection Rule:
   Evaluation condition: (vad_speech == True) AND (diar_label != "none")
   In Python code (reelcore/src/reelcore/edit/speaker.py:510):
     valid_eval = speech_mask & ((diar_a > 0.5) ^ (diar_b > 0.5))

2. Breakdown of Total Frames in agreement_breakdown_c1.txt:
   Total frames in reel_c1: 1200
   - vad_speech == True (speech detected by Silero VAD): 1014
   - vad_speech == False (silence / non-speech frames):   186
   - diar_label != "none" (transcript segment labeled):  1193
   - diar_label == "none" (transcript boundary gaps):     7

3. Evaluated Set (Denominator):
   Frames where vad_speech == True AND diar_label != "none":
   Denominator = 1007 frames

4. Matching Frames (Numerator):
   Within the 1007 evaluated frames:
   - agree == True  (fused_label == diar_label): 836 frames
   - agree == False (fused_label != diar_label): 171 frames

5. Exact Arithmetic:
   Agreement Rate = Numerator / Denominator
                  = 836 / 1007
                  = 0.830189
                  = 83.02%

6. Verification from agreement_breakdown_c1.txt:
   Grep verification:
     grep "True.*True" output/PHASE_3_CONVERSATIONAL/agreement_breakdown_c1.txt | grep -v "#" | wc -l
     matches exactly 836 rows where col 2 (vad_speech) is True and col 6 (agree) is True.
