Phase 3 §1.2 (ANALYZE_SPEAKERS) has resolved both Prompt 012 blocking items with raw artifacts: (1) Frame-by-frame analysis of c1 frames 650–682 (c1_boundary_650_682.txt) proves that the 24-frame 'both' run (frames 655–678) straddling the single speaker turn boundary at frame 662 is a MISS caused by the unconstrained mouth-AND sub-clause (mouth_a >= 0.20 and mouth_b >= 0.20) firing while transcript diarization indicates a clean single speaker turn (diar_b = 1.0, diar_a = 0.0) with score_b (0.5611–0.7562) dominating score_a (0.1144–0.1791); the proposed fix is to eliminate the unconstrained mouth-AND clause and require competitive composite scores (score-AND: sa >= threshold and sb >= threshold and abs(sa - sb) < overlap_thr) or gate mouth-overlap with diarization compatibility (not ((diar_a > 0.5) ^ (diar_b > 0.5))); and (2) The complete enumeration of all 204 raw pre-smoothing 'both' frames for c1 with their individual min(sa, sb) values is dumped to both_minscore_c1.txt, verifying the distribution min=0.1113, max=0.4789, mean=0.1875 with zero omissions. Execution stops at the review boundary without starting §1.3.

=== ITEM 1: C1 BOUNDARY 650-682 RAW DUMP & CLAUSE DIAGNOSIS ===

frame	mouth_a	mouth_b	diar_a	diar_b	sa	sb	is_both	clause	raw_label
 650	0.0128	0.3653	1.0000	0.0000	0.4570	0.2009	False	none      	spk_a
 651	0.0140	0.3712	1.0000	0.0000	0.4577	0.2042	False	none      	spk_a
 652	0.1539	0.3709	1.0000	0.0000	0.5346	0.2040	False	none      	spk_a
 653	0.1609	0.3934	1.0000	0.0000	0.5385	0.2163	False	none      	spk_a
 654	0.1623	0.4228	1.0000	0.0000	0.5393	0.2325	False	none      	spk_a
 655	0.3075	0.4221	0.0000	0.0000	0.1691	0.2322	True 	mouth-AND 	both
 656	0.3166	0.3869	0.0000	0.0000	0.1741	0.2128	True 	mouth-AND 	both
 657	0.3327	0.3596	0.0000	0.0000	0.1830	0.1978	True 	mouth-AND 	both
 658	0.4762	0.3314	0.0000	0.0000	0.2619	0.1822	True 	mouth-AND 	both
 659	0.4756	0.2926	0.0000	0.0000	0.2616	0.1609	True 	mouth-AND 	both
 660	0.4705	0.2592	0.0000	0.0000	0.2588	0.1426	True 	mouth-AND 	both
 661	0.4692	0.2476	0.0000	0.0000	0.2581	0.1362	True 	mouth-AND 	both
 662	0.3257	0.2019	0.0000	1.0000	0.1791	0.5611	True 	mouth-AND 	both
 663	0.3194	0.1954	0.0000	1.0000	0.1757	0.5575	False	none      	spk_b
 664	0.3033	0.2647	0.0000	1.0000	0.1668	0.5956	True 	mouth-AND 	both
 665	0.3017	0.3413	0.0000	1.0000	0.1659	0.6377	True 	mouth-AND 	both
 666	0.3016	0.4178	0.0000	1.0000	0.1659	0.6798	True 	mouth-AND 	both
 667	0.3001	0.4451	0.0000	1.0000	0.1650	0.6948	True 	mouth-AND 	both
 668	0.2989	0.4302	0.0000	1.0000	0.1644	0.6866	True 	mouth-AND 	both
 669	0.2967	0.4535	0.0000	1.0000	0.1632	0.6994	True 	mouth-AND 	both
 670	0.2949	0.4351	0.0000	1.0000	0.1622	0.6893	True 	mouth-AND 	both
 671	0.3037	0.4186	0.0000	1.0000	0.1670	0.6803	True 	mouth-AND 	both
 672	0.3036	0.4518	0.0000	1.0000	0.1670	0.6985	True 	mouth-AND 	both
 673	0.2080	0.4978	0.0000	1.0000	0.1144	0.7238	True 	mouth-AND 	both
 674	0.2539	0.5364	0.0000	1.0000	0.1397	0.7450	True 	mouth-AND 	both
 675	0.2836	0.5419	0.0000	1.0000	0.1560	0.7480	True 	mouth-AND 	both
 676	0.3052	0.5452	0.0000	1.0000	0.1678	0.7498	True 	mouth-AND 	both
 677	0.3095	0.5567	0.0000	1.0000	0.1702	0.7562	True 	mouth-AND 	both
 678	0.3115	0.5373	0.0000	1.0000	0.1713	0.7455	True 	mouth-AND 	both
 679	0.1940	0.4896	0.0000	1.0000	0.1067	0.7193	False	none      	spk_b
 680	0.1622	0.4275	0.0000	1.0000	0.0892	0.6851	False	none      	spk_b
 681	0.1383	0.3454	0.0000	1.0000	0.0761	0.6400	False	none      	spk_b
 682	0.1282	0.3466	0.0000	1.0000	0.0705	0.6406	False	none      	spk_b


--- DIAGNOSIS AND PROPOSED FIX ---
Finding: As shown above, every frame from 655 to 678 fired exclusively on clause 'mouth-AND' ((mouth_a >= 0.20 and mouth_b >= 0.20)).
At frames 662-678, diar_b = 1.0 (Dr. Saba's active turn) while diar_a = 0.0. Composite score sb is 0.5611 to 0.7562, whereas sa is only 0.1144 to 0.1791 (margin sb - sa reaches up to +0.6094).
Because mouth_a hovered around 0.20-0.32 due to listener breathing/facial movement, the raw threshold (mouth_a >= 0.20 and mouth_b >= 0.20) bypassed the weighted score fusion entirely and forced 'both', causing a 24-frame miss at the turn boundary. This is confirmed as a MISS, not true conversational overlap.

Proposed Fix:
Option A (Recommended): Remove the unconstrained mouth-AND clause entirely, relying on the evidence-fused 'score-AND' clause:
    is_both = (
        (diar_a[f] > 0.5 and diar_b[f] > 0.5)
        or (sa >= threshold and sb >= threshold and abs(sa - sb) < overlap_thr)
    )
This guarantees overlap is only triggered when both speakers have competitive composite evidence (|sa - sb| < 0.08, loaded from edit preset), preventing listener jaw motion from triggering false overlap against an active speaker turn.

Option B: Gate the mouth-AND clause by diarization compatibility:
    c_mouth = (mouth_a[f] >= 0.20 and mouth_b[f] >= 0.20) and not ((diar_a[f] > 0.5) ^ (diar_b[f] > 0.5))
This permits dual-mouth motion to declare overlap only when diarization does not strictly assert a single speaker turn.

=== ITEM 2: RAW BOTH_MINSCORE_C1.TXT (ALL 204 PRE-SMOOTHING BOTH FRAMES) ===

# Raw pre-smoothing both frames for c1 (total=204)
# Distribution: min=0.1113, max=0.4789, mean=0.1875
# frame	min_sa_sb
   5	0.1167
   6	0.2273
   7	0.3093
   8	0.3169
   9	0.2965
  10	0.2854
  20	0.3732
  21	0.3600
  22	0.2871
  23	0.2062
  24	0.1179
  30	0.1113
  31	0.1630
  32	0.1971
  33	0.2133
  34	0.2576
  35	0.2741
  38	0.1347
  51	0.1263
  52	0.1645
  53	0.2035
  54	0.2282
  55	0.2356
  56	0.2372
  64	0.1714
  65	0.1442
  87	0.1655
  93	0.1286
 129	0.1212
 130	0.1421
 131	0.1488
 132	0.1236
 182	0.2073
 183	0.2293
 184	0.2236
 185	0.3628
 186	0.3177
 187	0.2894
 188	0.2721
 199	0.4542
 200	0.4789
 201	0.4731
 202	0.4729
 249	0.1175
 250	0.1126
 251	0.1178
 254	0.1760
 255	0.1952
 256	0.2189
 257	0.2464
 258	0.2281
 259	0.1805
 260	0.1492
 261	0.1695
 262	0.1669
 264	0.1178
 269	0.1440
 270	0.1476
 285	0.1373
 287	0.1874
 291	0.2066
 293	0.2211
 297	0.2451
 298	0.2599
 299	0.2637
 300	0.2416
 301	0.2223
 302	0.2217
 303	0.2076
 310	0.2307
 311	0.2481
 312	0.2468
 313	0.2581
 314	0.2400
 315	0.1869
 316	0.1890
 317	0.1640
 318	0.1376
 336	0.2637
 337	0.2751
 338	0.2327
 339	0.2012
 340	0.1946
 341	0.1501
 342	0.1440
 343	0.1180
 348	0.1156
 460	0.1279
 461	0.1267
 462	0.1244
 463	0.1244
 464	0.1244
 465	0.1244
 466	0.1248
 484	0.2356
 485	0.1727
 486	0.1713
 487	0.1558
 488	0.2087
 489	0.1782
 490	0.1295
 491	0.1507
 492	0.1425
 493	0.1525
 494	0.1555
 498	0.1600
 499	0.1983
 500	0.2083
 501	0.2273
 502	0.2195
 503	0.2050
 504	0.1956
 655	0.1691
 656	0.1741
 657	0.1830
 658	0.1822
 659	0.1609
 660	0.1426
 661	0.1362
 662	0.1791
 664	0.1668
 665	0.1659
 666	0.1659
 667	0.1650
 668	0.1644
 669	0.1632
 670	0.1622
 671	0.1670
 672	0.1670
 673	0.1144
 674	0.1397
 675	0.1560
 676	0.1678
 677	0.1702
 678	0.1713
 684	0.1497
 685	0.1734
 686	0.1914
 687	0.2215
 688	0.2331
 689	0.2262
 690	0.2288
 691	0.1888
 692	0.1938
 693	0.1949
 694	0.1765
 695	0.1734
 696	0.1828
 697	0.1788
 698	0.2210
 699	0.2684
 700	0.2424
 701	0.2286
 702	0.2185
 703	0.2097
 704	0.2067
 705	0.1305
 709	0.1317
 710	0.1415
 711	0.1408
 712	0.1360
 713	0.1350
 714	0.1412
 715	0.1410
 719	0.1291
 720	0.1253
 721	0.1156
 722	0.1117
 770	0.1708
 771	0.1705
 772	0.1755
 773	0.1773
 774	0.1775
 822	0.1273
 823	0.1451
 824	0.1477
 846	0.2552
 847	0.2359
 848	0.2026
 849	0.1833
 850	0.1623
 851	0.1496
 852	0.2070
 853	0.1396
 854	0.1301
 855	0.1271
 856	0.1285
 857	0.1253
 858	0.1144
 901	0.1396
 902	0.1490
 915	0.1409
 916	0.1380
 917	0.1351
 918	0.1296
 919	0.1261
 921	0.1281
 922	0.1380
 923	0.1470
 924	0.1537
 925	0.1581
 926	0.1528
 927	0.1449
1085	0.1855
