=== ITEM 1: SPEAKER ATTRIBUTION REALITY & MOUTH VS DIARIZATION ON PRE-MIXED FOOTAGE ===
Plain technical reality: On this footage (single pre-mixed AAC stereo stream with Ch2 dead at -45.1 dB), mouth motion is effectively inert for speaker attribution. The previously reported 0.999 agreement rate was not an independent classification accuracy, but the fused timeline echoing its own dominant diarization prior (+0.45 binary prior with 0.45 weight vs 0.55 mouth weight, requiring mouth motion > 0.836 to ever overcome a disagreeing diarization label).
When evaluating all 1,007 active speech frames with diarization (diar_a XOR diar_b):
- Mouth winner disagrees with Diarization winner on 491 frames (48.8% of speech frames).
- Across those 491 disagreement frames, the fused timeline followed Diarization on 490 frames (99.8%) and followed Mouth on 0 frames (0.0%), with 1 frame falling below speech threshold to silence.
- This proves mouth optical motion does not meaningfully shift or validate active-speaker decisions on this footage; the active-speaker timeline is almost entirely driven by transcript diarization.

--- SUMMARY COUNTS: c1 SPEECH FRAMES (diar_a XOR diar_b) ---
N_speech_diar_frames: 1007
N where mouth_winner == diar_winner: 516 (51.2%)
N where mouth_winner != diar_winner: 491 (48.8%)
Of those disagreements (491 frames):
  N where fused followed diar:  490 (99.8%)
  N where fused followed mouth: 0 (0.0%)
  N where fused was silence:    1 (0.2%)

--- RAW PER-FRAME DATA: c1 SPEECH FRAMES (mouth_vs_diar_c1.txt) ---
frame	mouth_winner	diar_winner	fused_winner	agree_mouth_vs_diar
    0	spk_a       	spk_a      	spk_a       	True
    1	spk_a       	spk_a      	spk_a       	True
    2	spk_a       	spk_a      	spk_a       	True
    3	spk_a       	spk_a      	spk_a       	True
    4	spk_a       	spk_a      	spk_a       	True
    5	spk_a       	spk_a      	spk_a       	True
    6	spk_a       	spk_a      	spk_a       	True
    7	spk_b       	spk_a      	spk_a       	False
    8	spk_b       	spk_a      	spk_a       	False
    9	spk_b       	spk_a      	spk_a       	False
   10	spk_b       	spk_a      	spk_a       	False
   11	spk_b       	spk_a      	spk_a       	False
   12	spk_b       	spk_a      	spk_a       	False
   13	spk_b       	spk_a      	spk_a       	False
   14	spk_b       	spk_a      	spk_a       	False
   15	spk_b       	spk_a      	spk_a       	False
   16	spk_b       	spk_a      	spk_a       	False
   17	spk_b       	spk_a      	spk_a       	False
   18	spk_b       	spk_a      	spk_a       	False
   19	spk_b       	spk_a      	spk_a       	False
   20	spk_b       	spk_a      	spk_a       	False
   21	spk_b       	spk_a      	spk_a       	False
   22	spk_b       	spk_a      	spk_a       	False
   23	spk_b       	spk_a      	spk_a       	False
   24	spk_a       	spk_a      	spk_a       	True
   25	spk_a       	spk_a      	spk_a       	True
   26	spk_a       	spk_a      	spk_a       	True
   27	spk_a       	spk_a      	spk_a       	True
   28	spk_a       	spk_a      	spk_a       	True
   29	spk_a       	spk_a      	spk_a       	True
   30	spk_a       	spk_a      	spk_a       	True
   31	spk_a       	spk_a      	spk_a       	True
   32	spk_b       	spk_a      	spk_a       	False
   33	spk_b       	spk_a      	spk_a       	False
   34	spk_b       	spk_a      	spk_a       	False
   35	spk_b       	spk_a      	spk_a       	False
   36	spk_b       	spk_a      	spk_a       	False
   37	spk_b       	spk_a      	spk_a       	False
   38	spk_a       	spk_a      	spk_a       	True
   39	spk_a       	spk_a      	spk_a       	True
   40	spk_a       	spk_a      	spk_a       	True
   41	spk_a       	spk_a      	spk_a       	True
   42	spk_a       	spk_a      	spk_a       	True
   43	spk_a       	spk_a      	spk_a       	True
   44	spk_a       	spk_a      	spk_a       	True
   45	spk_a       	spk_a      	spk_a       	True
   46	spk_a       	spk_a      	spk_a       	True
   47	spk_a       	spk_a      	spk_a       	True
   48	spk_a       	spk_a      	spk_a       	True
   49	spk_a       	spk_a      	spk_a       	True
   50	spk_a       	spk_a      	spk_a       	True
   51	spk_a       	spk_a      	spk_a       	True
   52	spk_a       	spk_a      	spk_a       	True
   53	spk_b       	spk_a      	spk_a       	False
   54	spk_b       	spk_a      	spk_a       	False
   55	spk_b       	spk_a      	spk_a       	False
   56	spk_b       	spk_a      	spk_a       	False
   57	spk_b       	spk_a      	spk_a       	False
   58	spk_b       	spk_a      	spk_a       	False
   59	spk_b       	spk_a      	spk_a       	False
   60	spk_b       	spk_a      	spk_a       	False
   61	spk_b       	spk_a      	spk_a       	False
   62	spk_b       	spk_a      	spk_a       	False
   63	spk_b       	spk_a      	spk_a       	False
   64	spk_b       	spk_a      	spk_a       	False
   65	spk_b       	spk_a      	spk_a       	False
   66	spk_a       	spk_a      	spk_a       	True
   67	spk_a       	spk_a      	spk_a       	True
   68	spk_a       	spk_a      	spk_a       	True
   78	spk_b       	spk_a      	spk_a       	False
   79	spk_b       	spk_a      	spk_a       	False
   80	spk_b       	spk_a      	spk_a       	False
   81	spk_b       	spk_a      	spk_a       	False
   82	spk_b       	spk_a      	spk_a       	False
   83	spk_b       	spk_a      	spk_a       	False
   84	spk_b       	spk_a      	spk_a       	False
   85	spk_b       	spk_a      	spk_a       	False
   86	spk_b       	spk_a      	spk_a       	False
   87	spk_b       	spk_a      	spk_a       	False
   88	spk_a       	spk_a      	spk_a       	True
   89	spk_a       	spk_a      	spk_a       	True
   90	spk_a       	spk_a      	spk_a       	True
   91	spk_a       	spk_a      	spk_a       	True
   92	spk_a       	spk_a      	spk_a       	True
   93	spk_b       	spk_a      	spk_a       	False
   94	spk_b       	spk_a      	spk_a       	False
   95	spk_b       	spk_a      	spk_a       	False
   96	spk_b       	spk_a      	spk_a       	False
   97	spk_b       	spk_a      	spk_a       	False
   98	spk_b       	spk_a      	spk_a       	False
   99	spk_b       	spk_a      	spk_a       	False
  100	spk_b       	spk_a      	spk_a       	False
  101	spk_b       	spk_a      	spk_a       	False
  102	spk_b       	spk_a      	spk_a       	False
  103	spk_b       	spk_a      	spk_a       	False
  104	spk_b       	spk_a      	spk_a       	False
  105	spk_b       	spk_a      	spk_a       	False
  106	spk_b       	spk_a      	spk_a       	False
  107	spk_b       	spk_a      	spk_a       	False
  120	spk_a       	spk_a      	spk_a       	True
  121	spk_a       	spk_a      	spk_a       	True
  122	spk_a       	spk_a      	spk_a       	True
  123	spk_a       	spk_a      	spk_a       	True
  124	spk_a       	spk_a      	spk_a       	True
  125	spk_a       	spk_a      	spk_a       	True
  126	spk_a       	spk_a      	spk_a       	True
  127	spk_a       	spk_a      	spk_a       	True
  128	spk_a       	spk_a      	spk_a       	True
  129	spk_a       	spk_a      	spk_a       	True
  130	spk_a       	spk_a      	spk_a       	True
  131	spk_a       	spk_a      	spk_a       	True
  132	spk_a       	spk_a      	spk_a       	True
  133	spk_a       	spk_a      	spk_a       	True
  134	spk_a       	spk_a      	spk_a       	True
  135	spk_a       	spk_a      	spk_a       	True
  136	spk_a       	spk_a      	spk_a       	True
  137	spk_a       	spk_a      	spk_a       	True
  138	spk_a       	spk_a      	spk_a       	True
  139	spk_a       	spk_a      	spk_a       	True
  140	spk_a       	spk_a      	spk_a       	True
  141	spk_a       	spk_a      	spk_a       	True
  142	spk_a       	spk_a      	spk_a       	True
  143	spk_a       	spk_a      	spk_a       	True
  144	spk_a       	spk_a      	spk_a       	True
  145	spk_a       	spk_a      	spk_a       	True
  146	spk_a       	spk_a      	spk_a       	True
  147	spk_a       	spk_a      	spk_a       	True
  148	spk_a       	spk_a      	spk_a       	True
  149	spk_a       	spk_a      	spk_a       	True
  150	spk_a       	spk_a      	spk_a       	True
  151	spk_a       	spk_a      	spk_a       	True
  152	spk_a       	spk_a      	spk_a       	True
  153	spk_a       	spk_a      	spk_a       	True
  154	spk_a       	spk_a      	spk_a       	True
  155	spk_a       	spk_a      	spk_a       	True
  156	spk_a       	spk_a      	spk_a       	True
  157	spk_a       	spk_a      	spk_a       	True
  158	spk_a       	spk_a      	spk_a       	True
  159	spk_a       	spk_a      	spk_a       	True
  160	spk_a       	spk_a      	spk_a       	True
  161	spk_a       	spk_a      	spk_a       	True
  162	spk_a       	spk_a      	spk_a       	True
  163	spk_b       	spk_a      	spk_a       	False
  164	spk_b       	spk_a      	spk_a       	False
  165	spk_b       	spk_a      	spk_a       	False
  166	spk_b       	spk_a      	spk_a       	False
  167	spk_b       	spk_a      	spk_a       	False
  168	spk_b       	spk_a      	spk_a       	False
  169	spk_b       	spk_a      	spk_a       	False
  170	spk_b       	spk_a      	spk_a       	False
  171	spk_b       	spk_a      	spk_a       	False
  172	spk_a       	spk_a      	spk_a       	True
  173	spk_a       	spk_a      	spk_a       	True
  174	spk_a       	spk_a      	spk_a       	True
  175	spk_b       	spk_a      	spk_a       	False
  176	spk_b       	spk_a      	spk_a       	False
  177	spk_b       	spk_a      	spk_a       	False
  178	spk_b       	spk_a      	spk_a       	False
  179	spk_b       	spk_a      	spk_a       	False
  180	spk_b       	spk_a      	spk_a       	False
  181	spk_b       	spk_a      	spk_a       	False
  182	spk_b       	spk_a      	spk_a       	False
  183	spk_b       	spk_a      	spk_a       	False
  184	spk_b       	spk_a      	spk_a       	False
  185	spk_b       	spk_a      	spk_a       	False
  186	spk_b       	spk_a      	spk_a       	False
  187	spk_b       	spk_a      	spk_a       	False
  188	spk_b       	spk_a      	spk_a       	False
  189	spk_b       	spk_a      	spk_a       	False
  190	spk_b       	spk_a      	spk_a       	False
  191	spk_b       	spk_a      	spk_a       	False
  192	spk_b       	spk_a      	spk_a       	False
  193	spk_b       	spk_a      	spk_a       	False
  194	spk_b       	spk_a      	spk_a       	False
  195	spk_b       	spk_a      	spk_a       	False
  196	spk_b       	spk_a      	spk_a       	False
  197	spk_b       	spk_a      	spk_a       	False
  198	spk_b       	spk_a      	spk_a       	False
  199	spk_b       	spk_a      	spk_a       	False
  200	spk_b       	spk_a      	spk_a       	False
  201	spk_b       	spk_a      	spk_a       	False
  202	spk_b       	spk_a      	spk_a       	False
  203	spk_b       	spk_a      	spk_a       	False
  204	spk_b       	spk_a      	spk_a       	False
  205	spk_b       	spk_a      	spk_a       	False
  206	spk_b       	spk_a      	spk_a       	False
  207	spk_b       	spk_a      	spk_a       	False
  208	spk_b       	spk_a      	spk_a       	False
  209	spk_b       	spk_a      	spk_a       	False
  219	spk_b       	spk_a      	spk_a       	False
  220	spk_a       	spk_a      	spk_a       	True
  221	spk_a       	spk_a      	spk_a       	True
  222	spk_a       	spk_a      	spk_a       	True
  223	spk_a       	spk_a      	spk_a       	True
  224	spk_a       	spk_a      	spk_a       	True
  225	spk_a       	spk_a      	spk_a       	True
  226	spk_a       	spk_a      	spk_a       	True
  227	spk_b       	spk_a      	spk_a       	False
  228	spk_b       	spk_a      	spk_a       	False
  229	spk_b       	spk_a      	spk_a       	False
  230	spk_b       	spk_a      	spk_a       	False
  231	spk_b       	spk_a      	spk_a       	False
  232	spk_b       	spk_a      	spk_a       	False
  233	spk_b       	spk_a      	spk_a       	False
  234	spk_b       	spk_a      	spk_a       	False
  235	spk_b       	spk_a      	spk_a       	False
  236	spk_b       	spk_a      	spk_a       	False
  237	spk_b       	spk_a      	spk_a       	False
  238	spk_b       	spk_a      	spk_a       	False
  239	spk_b       	spk_a      	spk_a       	False
  240	spk_b       	spk_a      	spk_a       	False
  241	spk_b       	spk_a      	spk_a       	False
  242	spk_b       	spk_a      	spk_a       	False
  243	spk_b       	spk_a      	spk_a       	False
  244	spk_b       	spk_a      	spk_a       	False
  245	spk_b       	spk_a      	spk_a       	False
  246	spk_a       	spk_a      	spk_a       	True
  247	spk_a       	spk_a      	spk_a       	True
  248	spk_a       	spk_a      	spk_a       	True
  249	spk_a       	spk_a      	spk_a       	True
  250	spk_a       	spk_a      	spk_a       	True
  251	spk_a       	spk_a      	spk_a       	True
  252	spk_b       	spk_a      	spk_a       	False
  253	spk_b       	spk_a      	spk_a       	False
  254	spk_b       	spk_a      	spk_a       	False
  255	spk_b       	spk_a      	spk_a       	False
  256	spk_b       	spk_a      	spk_a       	False
  257	spk_b       	spk_a      	spk_a       	False
  258	spk_b       	spk_a      	spk_a       	False
  259	spk_a       	spk_a      	spk_a       	True
  260	spk_a       	spk_a      	spk_a       	True
  261	spk_b       	spk_a      	spk_a       	False
  262	spk_b       	spk_a      	spk_a       	False
  263	spk_b       	spk_a      	spk_a       	False
  264	spk_b       	spk_a      	spk_a       	False
  265	spk_b       	spk_a      	spk_a       	False
  266	spk_b       	spk_a      	spk_a       	False
  267	spk_b       	spk_a      	spk_a       	False
  268	spk_b       	spk_a      	spk_a       	False
  269	spk_b       	spk_a      	spk_a       	False
  270	spk_b       	spk_a      	spk_a       	False
  271	spk_b       	spk_a      	spk_a       	False
  272	spk_b       	spk_a      	spk_a       	False
  273	spk_b       	spk_a      	spk_a       	False
  274	spk_b       	spk_a      	spk_a       	False
  275	spk_b       	spk_a      	spk_a       	False
  276	spk_b       	spk_a      	spk_a       	False
  277	spk_b       	spk_a      	spk_a       	False
  278	spk_b       	spk_a      	spk_a       	False
  279	spk_b       	spk_a      	spk_a       	False
  280	spk_b       	spk_a      	spk_a       	False
  281	spk_a       	spk_a      	spk_a       	True
  282	spk_a       	spk_a      	spk_a       	True
  283	spk_b       	spk_a      	spk_a       	False
  284	spk_b       	spk_a      	spk_a       	False
  285	spk_b       	spk_a      	spk_a       	False
  286	spk_b       	spk_a      	spk_a       	False
  287	spk_b       	spk_a      	spk_a       	False
  288	spk_b       	spk_a      	spk_a       	False
  289	spk_b       	spk_a      	spk_a       	False
  290	spk_b       	spk_a      	spk_a       	False
  291	spk_b       	spk_a      	spk_a       	False
  292	spk_b       	spk_a      	spk_a       	False
  293	spk_b       	spk_a      	spk_a       	False
  294	spk_b       	spk_a      	spk_a       	False
  295	spk_b       	spk_a      	spk_a       	False
  296	spk_b       	spk_a      	spk_a       	False
  297	spk_b       	spk_a      	spk_a       	False
  298	spk_b       	spk_a      	spk_a       	False
  299	spk_b       	spk_a      	spk_a       	False
  300	spk_b       	spk_a      	spk_a       	False
  301	spk_b       	spk_a      	spk_a       	False
  302	spk_b       	spk_a      	spk_a       	False
  303	spk_b       	spk_a      	spk_a       	False
  304	spk_b       	spk_a      	spk_a       	False
  305	spk_b       	spk_a      	spk_a       	False
  306	spk_b       	spk_a      	spk_a       	False
  307	spk_b       	spk_a      	spk_a       	False
  308	spk_b       	spk_a      	spk_a       	False
  309	spk_b       	spk_a      	spk_a       	False
  310	spk_b       	spk_a      	spk_a       	False
  311	spk_b       	spk_a      	spk_a       	False
  312	spk_b       	spk_a      	spk_a       	False
  313	spk_b       	spk_a      	spk_a       	False
  314	spk_b       	spk_a      	spk_a       	False
  315	spk_a       	spk_a      	spk_a       	True
  316	spk_b       	spk_a      	spk_a       	False
  317	spk_b       	spk_a      	spk_a       	False
  318	spk_b       	spk_a      	spk_a       	False
  319	spk_b       	spk_a      	spk_a       	False
  320	spk_b       	spk_a      	spk_a       	False
  321	spk_a       	spk_a      	spk_a       	True
  322	spk_a       	spk_a      	spk_a       	True
  323	spk_a       	spk_a      	spk_a       	True
  324	spk_a       	spk_a      	spk_a       	True
  325	spk_a       	spk_a      	spk_a       	True
  326	spk_a       	spk_a      	spk_a       	True
  327	spk_a       	spk_a      	spk_a       	True
  328	spk_a       	spk_a      	spk_a       	True
  329	spk_b       	spk_a      	spk_a       	False
  330	spk_b       	spk_a      	spk_a       	False
  331	spk_b       	spk_a      	spk_a       	False
  332	spk_b       	spk_a      	spk_a       	False
  333	spk_b       	spk_a      	spk_a       	False
  334	spk_b       	spk_a      	spk_a       	False
  335	spk_b       	spk_a      	spk_a       	False
  336	spk_b       	spk_a      	spk_a       	False
  337	spk_b       	spk_a      	spk_a       	False
  338	spk_b       	spk_a      	spk_a       	False
  339	spk_b       	spk_a      	spk_a       	False
  340	spk_b       	spk_a      	spk_a       	False
  341	spk_b       	spk_a      	spk_a       	False
  342	spk_b       	spk_a      	spk_a       	False
  343	spk_b       	spk_a      	spk_a       	False
  344	spk_a       	spk_a      	spk_a       	True
  345	spk_a       	spk_a      	spk_a       	True
  346	spk_a       	spk_a      	spk_a       	True
  347	spk_a       	spk_a      	spk_a       	True
  348	spk_b       	spk_a      	spk_a       	False
  349	spk_b       	spk_a      	spk_a       	False
  350	spk_b       	spk_a      	spk_a       	False
  351	spk_b       	spk_a      	spk_a       	False
  352	spk_b       	spk_a      	spk_a       	False
  353	spk_b       	spk_a      	spk_a       	False
  354	spk_b       	spk_a      	spk_a       	False
  355	spk_b       	spk_a      	spk_a       	False
  356	spk_b       	spk_a      	spk_a       	False
  357	spk_b       	spk_a      	spk_a       	False
  358	spk_b       	spk_a      	spk_a       	False
  359	spk_b       	spk_a      	spk_a       	False
  375	spk_b       	spk_a      	spk_a       	False
  376	spk_b       	spk_a      	spk_a       	False
  377	spk_b       	spk_a      	spk_a       	False
  378	spk_b       	spk_a      	spk_a       	False
  379	spk_b       	spk_a      	spk_a       	False
  380	spk_b       	spk_a      	spk_a       	False
  381	spk_b       	spk_a      	spk_a       	False
  382	spk_b       	spk_a      	spk_a       	False
  383	spk_b       	spk_a      	spk_a       	False
  384	spk_b       	spk_a      	spk_a       	False
  385	spk_b       	spk_a      	spk_a       	False
  386	spk_b       	spk_a      	spk_a       	False
  387	spk_b       	spk_a      	spk_a       	False
  388	spk_b       	spk_a      	spk_a       	False
  389	spk_b       	spk_a      	spk_a       	False
  390	spk_b       	spk_a      	spk_a       	False
  391	spk_b       	spk_a      	spk_a       	False
  392	spk_b       	spk_a      	spk_a       	False
  393	spk_b       	spk_a      	spk_a       	False
  394	spk_b       	spk_a      	spk_a       	False
  395	spk_b       	spk_a      	spk_a       	False
  396	spk_b       	spk_a      	spk_a       	False
  397	spk_b       	spk_a      	spk_a       	False
  398	spk_b       	spk_a      	spk_a       	False
  399	spk_b       	spk_a      	spk_a       	False
  400	spk_b       	spk_a      	spk_a       	False
  401	spk_b       	spk_a      	spk_a       	False
  402	spk_b       	spk_a      	spk_a       	False
  403	spk_b       	spk_a      	spk_a       	False
  404	spk_b       	spk_a      	spk_a       	False
  405	spk_b       	spk_a      	spk_a       	False
  406	spk_b       	spk_a      	spk_a       	False
  407	spk_b       	spk_a      	spk_a       	False
  408	spk_b       	spk_a      	spk_a       	False
  409	spk_b       	spk_a      	spk_a       	False
  410	spk_b       	spk_a      	spk_a       	False
  411	spk_b       	spk_a      	spk_a       	False
  412	spk_b       	spk_a      	spk_a       	False
  413	spk_b       	spk_a      	spk_a       	False
  414	spk_b       	spk_a      	spk_a       	False
  415	spk_b       	spk_a      	spk_a       	False
  416	spk_b       	spk_a      	spk_a       	False
  417	spk_b       	spk_a      	spk_a       	False
  418	spk_b       	spk_a      	spk_a       	False
  419	spk_b       	spk_a      	spk_a       	False
  423	spk_b       	spk_a      	spk_a       	False
  424	spk_b       	spk_a      	spk_a       	False
  425	spk_b       	spk_a      	spk_a       	False
  426	spk_b       	spk_a      	spk_a       	False
  427	spk_b       	spk_a      	spk_a       	False
  428	spk_b       	spk_a      	spk_a       	False
  429	spk_b       	spk_a      	spk_a       	False
  430	spk_b       	spk_a      	spk_a       	False
  431	spk_b       	spk_a      	spk_a       	False
  432	spk_b       	spk_a      	spk_a       	False
  433	spk_b       	spk_a      	spk_a       	False
  434	spk_b       	spk_a      	spk_a       	False
  435	spk_b       	spk_a      	spk_a       	False
  436	spk_b       	spk_a      	spk_a       	False
  437	spk_b       	spk_a      	spk_a       	False
  438	spk_b       	spk_a      	spk_a       	False
  439	spk_b       	spk_a      	spk_a       	False
  440	spk_a       	spk_a      	spk_a       	True
  441	spk_a       	spk_a      	spk_a       	True
  442	spk_a       	spk_a      	spk_a       	True
  443	spk_a       	spk_a      	spk_a       	True
  444	spk_a       	spk_a      	spk_a       	True
  445	spk_a       	spk_a      	spk_a       	True
  446	spk_a       	spk_a      	spk_a       	True
  447	spk_a       	spk_a      	spk_a       	True
  448	spk_a       	spk_a      	spk_a       	True
  449	spk_a       	spk_a      	spk_a       	True
  450	spk_a       	spk_a      	spk_a       	True
  451	spk_a       	spk_a      	spk_a       	True
  452	spk_b       	spk_a      	spk_a       	False
  453	spk_b       	spk_a      	spk_a       	False
  454	spk_b       	spk_a      	spk_a       	False
  455	spk_a       	spk_a      	spk_a       	True
  456	spk_a       	spk_a      	spk_a       	True
  457	spk_a       	spk_a      	spk_a       	True
  458	spk_a       	spk_a      	spk_a       	True
  459	spk_a       	spk_a      	spk_a       	True
  460	spk_a       	spk_a      	spk_a       	True
  461	spk_a       	spk_a      	spk_a       	True
  462	spk_a       	spk_a      	spk_a       	True
  463	spk_a       	spk_a      	spk_a       	True
  464	spk_a       	spk_a      	spk_a       	True
  465	spk_a       	spk_a      	spk_a       	True
  466	spk_a       	spk_a      	spk_a       	True
  467	spk_a       	spk_a      	spk_a       	True
  468	spk_a       	spk_a      	spk_a       	True
  469	spk_a       	spk_a      	spk_a       	True
  470	spk_a       	spk_a      	spk_a       	True
  471	spk_a       	spk_a      	spk_a       	True
  472	spk_a       	spk_a      	spk_a       	True
  473	spk_a       	spk_a      	spk_a       	True
  474	spk_a       	spk_a      	spk_a       	True
  475	spk_a       	spk_a      	spk_a       	True
  476	spk_a       	spk_a      	spk_a       	True
  477	spk_a       	spk_a      	spk_a       	True
  478	spk_a       	spk_a      	spk_a       	True
  479	spk_a       	spk_a      	spk_a       	True
  480	spk_b       	spk_a      	spk_a       	False
  481	spk_b       	spk_a      	spk_a       	False
  482	spk_b       	spk_a      	spk_a       	False
  483	spk_b       	spk_a      	spk_a       	False
  484	spk_b       	spk_a      	spk_a       	False
  485	spk_a       	spk_a      	spk_a       	True
  486	spk_a       	spk_a      	spk_a       	True
  487	spk_a       	spk_a      	spk_a       	True
  488	spk_b       	spk_a      	spk_a       	False
  489	spk_a       	spk_a      	spk_a       	True
  490	spk_a       	spk_a      	spk_a       	True
  491	spk_b       	spk_a      	spk_a       	False
  492	spk_b       	spk_a      	spk_a       	False
  493	spk_b       	spk_a      	spk_a       	False
  494	spk_b       	spk_a      	spk_a       	False
  495	spk_a       	spk_a      	spk_a       	True
  496	spk_a       	spk_a      	spk_a       	True
  497	spk_a       	spk_a      	spk_a       	True
  498	spk_a       	spk_a      	spk_a       	True
  499	spk_b       	spk_a      	spk_a       	False
  500	spk_b       	spk_a      	spk_a       	False
  501	spk_b       	spk_a      	spk_a       	False
  502	spk_b       	spk_a      	spk_a       	False
  503	spk_b       	spk_a      	spk_a       	False
  504	spk_b       	spk_a      	spk_a       	False
  505	spk_a       	spk_a      	spk_a       	True
  506	spk_a       	spk_a      	spk_a       	True
  507	spk_a       	spk_a      	spk_a       	True
  508	spk_a       	spk_a      	spk_a       	True
  509	spk_a       	spk_a      	spk_a       	True
  510	spk_a       	spk_a      	spk_a       	True
  511	spk_a       	spk_a      	spk_a       	True
  512	spk_a       	spk_a      	spk_a       	True
  513	spk_a       	spk_a      	spk_a       	True
  514	spk_a       	spk_a      	spk_a       	True
  515	spk_a       	spk_a      	spk_a       	True
  516	spk_b       	spk_a      	spk_a       	False
  517	spk_b       	spk_a      	spk_a       	False
  518	spk_b       	spk_a      	spk_a       	False
  519	spk_b       	spk_a      	spk_a       	False
  520	spk_b       	spk_a      	spk_a       	False
  521	spk_b       	spk_a      	spk_a       	False
  522	spk_b       	spk_a      	spk_a       	False
  523	spk_b       	spk_a      	spk_a       	False
  524	spk_b       	spk_a      	spk_a       	False
  525	spk_b       	spk_a      	spk_a       	False
  526	spk_b       	spk_a      	spk_a       	False
  527	spk_b       	spk_a      	spk_a       	False
  528	spk_b       	spk_a      	spk_a       	False
  529	spk_b       	spk_a      	spk_a       	False
  530	spk_b       	spk_a      	spk_a       	False
  531	spk_b       	spk_a      	spk_a       	False
  532	spk_b       	spk_a      	spk_a       	False
  533	spk_b       	spk_a      	spk_a       	False
  534	spk_b       	spk_a      	spk_a       	False
  535	spk_b       	spk_a      	spk_a       	False
  536	spk_b       	spk_a      	spk_a       	False
  537	spk_b       	spk_a      	spk_a       	False
  538	spk_b       	spk_a      	spk_a       	False
  539	spk_b       	spk_a      	spk_a       	False
  540	spk_b       	spk_a      	spk_a       	False
  541	spk_b       	spk_a      	spk_a       	False
  542	spk_b       	spk_a      	spk_a       	False
  543	spk_b       	spk_a      	spk_a       	False
  544	spk_a       	spk_a      	spk_a       	True
  545	spk_a       	spk_a      	spk_a       	True
  546	spk_a       	spk_a      	spk_a       	True
  547	spk_a       	spk_a      	spk_a       	True
  548	spk_a       	spk_a      	spk_a       	True
  549	spk_a       	spk_a      	spk_a       	True
  550	spk_a       	spk_a      	spk_a       	True
  551	spk_a       	spk_a      	spk_a       	True
  552	spk_a       	spk_a      	spk_a       	True
  553	spk_a       	spk_a      	spk_a       	True
  554	spk_a       	spk_a      	spk_a       	True
  555	spk_a       	spk_a      	spk_a       	True
  556	spk_a       	spk_a      	spk_a       	True
  557	spk_a       	spk_a      	spk_a       	True
  558	spk_a       	spk_a      	spk_a       	True
  559	spk_b       	spk_a      	spk_a       	False
  560	spk_b       	spk_a      	spk_a       	False
  561	spk_b       	spk_a      	spk_a       	False
  562	spk_b       	spk_a      	spk_a       	False
  563	spk_b       	spk_a      	spk_a       	False
  579	spk_b       	spk_a      	spk_a       	False
  580	spk_b       	spk_a      	spk_a       	False
  581	spk_b       	spk_a      	spk_a       	False
  582	spk_b       	spk_a      	spk_a       	False
  583	spk_b       	spk_a      	spk_a       	False
  584	spk_b       	spk_a      	spk_a       	False
  585	spk_b       	spk_a      	spk_a       	False
  586	spk_b       	spk_a      	spk_a       	False
  587	spk_b       	spk_a      	spk_a       	False
  588	spk_b       	spk_a      	spk_a       	False
  589	spk_a       	spk_a      	spk_a       	True
  590	spk_a       	spk_a      	spk_a       	True
  591	spk_a       	spk_a      	spk_a       	True
  592	spk_a       	spk_a      	spk_a       	True
  593	spk_b       	spk_a      	spk_a       	False
  594	spk_b       	spk_a      	spk_a       	False
  595	spk_b       	spk_a      	spk_a       	False
  596	spk_b       	spk_a      	spk_a       	False
  597	spk_b       	spk_a      	spk_a       	False
  598	spk_b       	spk_a      	spk_a       	False
  599	spk_b       	spk_a      	spk_a       	False
  600	spk_b       	spk_a      	spk_a       	False
  601	spk_b       	spk_a      	spk_a       	False
  602	spk_b       	spk_a      	spk_a       	False
  603	spk_b       	spk_a      	spk_a       	False
  604	spk_b       	spk_a      	spk_a       	False
  605	spk_b       	spk_a      	spk_a       	False
  606	spk_b       	spk_a      	spk_a       	False
  607	spk_b       	spk_a      	spk_a       	False
  608	spk_a       	spk_a      	spk_a       	True
  609	spk_a       	spk_a      	spk_a       	True
  610	spk_a       	spk_a      	spk_a       	True
  611	spk_a       	spk_a      	spk_a       	True
  612	spk_a       	spk_a      	spk_a       	True
  613	spk_a       	spk_a      	spk_a       	True
  614	spk_a       	spk_a      	spk_a       	True
  615	spk_a       	spk_a      	spk_a       	True
  616	spk_a       	spk_a      	spk_a       	True
  617	spk_a       	spk_a      	spk_a       	True
  618	spk_b       	spk_a      	spk_a       	False
  619	spk_b       	spk_a      	spk_a       	False
  620	spk_b       	spk_a      	spk_a       	False
  621	spk_b       	spk_a      	spk_a       	False
  622	spk_a       	spk_a      	spk_a       	True
  623	spk_a       	spk_a      	spk_a       	True
  624	spk_a       	spk_a      	spk_a       	True
  625	spk_a       	spk_a      	spk_a       	True
  626	spk_a       	spk_a      	spk_a       	True
  627	spk_a       	spk_a      	spk_a       	True
  628	spk_a       	spk_a      	spk_a       	True
  629	spk_a       	spk_a      	spk_a       	True
  630	spk_a       	spk_a      	spk_a       	True
  631	spk_a       	spk_a      	spk_a       	True
  632	spk_a       	spk_a      	spk_a       	True
  651	spk_b       	spk_a      	silence     	False
  652	spk_b       	spk_a      	spk_a       	False
  653	spk_b       	spk_a      	spk_a       	False
  654	spk_b       	spk_a      	spk_a       	False
  662	spk_a       	spk_b      	spk_b       	False
  663	spk_a       	spk_b      	spk_b       	False
  664	spk_a       	spk_b      	spk_b       	False
  665	spk_b       	spk_b      	spk_b       	True
  666	spk_b       	spk_b      	spk_b       	True
  667	spk_b       	spk_b      	spk_b       	True
  668	spk_b       	spk_b      	spk_b       	True
  669	spk_b       	spk_b      	spk_b       	True
  670	spk_b       	spk_b      	spk_b       	True
  671	spk_b       	spk_b      	spk_b       	True
  672	spk_b       	spk_b      	spk_b       	True
  673	spk_b       	spk_b      	spk_b       	True
  674	spk_b       	spk_b      	spk_b       	True
  675	spk_b       	spk_b      	spk_b       	True
  676	spk_b       	spk_b      	spk_b       	True
  677	spk_b       	spk_b      	spk_b       	True
  678	spk_b       	spk_b      	spk_b       	True
  679	spk_b       	spk_b      	spk_b       	True
  680	spk_b       	spk_b      	spk_b       	True
  681	spk_b       	spk_b      	spk_b       	True
  682	spk_b       	spk_b      	spk_b       	True
  683	spk_b       	spk_b      	spk_b       	True
  684	spk_b       	spk_b      	spk_b       	True
  685	spk_b       	spk_b      	spk_b       	True
  686	spk_b       	spk_b      	spk_b       	True
  690	spk_b       	spk_b      	spk_b       	True
  691	spk_b       	spk_b      	spk_b       	True
  692	spk_b       	spk_b      	spk_b       	True
  693	spk_b       	spk_b      	spk_b       	True
  694	spk_b       	spk_b      	spk_b       	True
  695	spk_b       	spk_b      	spk_b       	True
  696	spk_b       	spk_b      	spk_b       	True
  697	spk_b       	spk_b      	spk_b       	True
  698	spk_b       	spk_b      	spk_b       	True
  699	spk_b       	spk_b      	spk_b       	True
  700	spk_b       	spk_b      	spk_b       	True
  701	spk_b       	spk_b      	spk_b       	True
  702	spk_b       	spk_b      	spk_b       	True
  703	spk_b       	spk_b      	spk_b       	True
  704	spk_b       	spk_b      	spk_b       	True
  705	spk_b       	spk_b      	spk_b       	True
  706	spk_b       	spk_b      	spk_b       	True
  707	spk_b       	spk_b      	spk_b       	True
  708	spk_b       	spk_b      	spk_b       	True
  709	spk_b       	spk_b      	spk_b       	True
  710	spk_b       	spk_b      	spk_b       	True
  711	spk_b       	spk_b      	spk_b       	True
  712	spk_b       	spk_b      	spk_b       	True
  713	spk_a       	spk_b      	spk_b       	False
  714	spk_a       	spk_b      	spk_b       	False
  715	spk_a       	spk_b      	spk_b       	False
  716	spk_a       	spk_b      	spk_b       	False
  717	spk_a       	spk_b      	spk_b       	False
  718	spk_a       	spk_b      	spk_b       	False
  719	spk_a       	spk_b      	spk_b       	False
  720	spk_b       	spk_b      	spk_b       	True
  721	spk_b       	spk_b      	spk_b       	True
  722	spk_b       	spk_b      	spk_b       	True
  723	spk_b       	spk_b      	spk_b       	True
  724	spk_b       	spk_b      	spk_b       	True
  725	spk_b       	spk_b      	spk_b       	True
  735	spk_b       	spk_b      	spk_b       	True
  736	spk_b       	spk_b      	spk_b       	True
  737	spk_b       	spk_b      	spk_b       	True
  738	spk_b       	spk_b      	spk_b       	True
  739	spk_b       	spk_b      	spk_b       	True
  740	spk_b       	spk_b      	spk_b       	True
  741	spk_b       	spk_b      	spk_b       	True
  742	spk_b       	spk_b      	spk_b       	True
  743	spk_b       	spk_b      	spk_b       	True
  744	spk_b       	spk_b      	spk_b       	True
  745	spk_b       	spk_b      	spk_b       	True
  746	spk_b       	spk_b      	spk_b       	True
  747	spk_b       	spk_b      	spk_b       	True
  748	spk_b       	spk_b      	spk_b       	True
  749	spk_b       	spk_b      	spk_b       	True
  750	spk_b       	spk_b      	spk_b       	True
  751	spk_b       	spk_b      	spk_b       	True
  752	spk_b       	spk_b      	spk_b       	True
  753	spk_b       	spk_b      	spk_b       	True
  754	spk_b       	spk_b      	spk_b       	True
  755	spk_b       	spk_b      	spk_b       	True
  756	spk_b       	spk_b      	spk_b       	True
  757	spk_b       	spk_b      	spk_b       	True
  758	spk_b       	spk_b      	spk_b       	True
  759	spk_b       	spk_b      	spk_b       	True
  760	spk_b       	spk_b      	spk_b       	True
  761	spk_b       	spk_b      	spk_b       	True
  762	spk_b       	spk_b      	spk_b       	True
  763	spk_b       	spk_b      	spk_b       	True
  764	spk_b       	spk_b      	spk_b       	True
  765	spk_b       	spk_b      	spk_b       	True
  766	spk_b       	spk_b      	spk_b       	True
  767	spk_b       	spk_b      	spk_b       	True
  768	spk_b       	spk_b      	spk_b       	True
  769	spk_b       	spk_b      	spk_b       	True
  770	spk_a       	spk_b      	spk_b       	False
  771	spk_b       	spk_b      	spk_b       	True
  772	spk_b       	spk_b      	spk_b       	True
  773	spk_b       	spk_b      	spk_b       	True
  774	spk_b       	spk_b      	spk_b       	True
  775	spk_b       	spk_b      	spk_b       	True
  776	spk_b       	spk_b      	spk_b       	True
  777	spk_b       	spk_b      	spk_b       	True
  778	spk_b       	spk_b      	spk_b       	True
  779	spk_b       	spk_b      	spk_b       	True
  780	spk_b       	spk_b      	spk_b       	True
  781	spk_b       	spk_b      	spk_b       	True
  782	spk_b       	spk_b      	spk_b       	True
  783	spk_b       	spk_b      	spk_b       	True
  784	spk_b       	spk_b      	spk_b       	True
  785	spk_b       	spk_b      	spk_b       	True
  786	spk_b       	spk_b      	spk_b       	True
  787	spk_b       	spk_b      	spk_b       	True
  788	spk_b       	spk_b      	spk_b       	True
  789	spk_b       	spk_b      	spk_b       	True
  790	spk_b       	spk_b      	spk_b       	True
  791	spk_b       	spk_b      	spk_b       	True
  792	spk_b       	spk_b      	spk_b       	True
  793	spk_b       	spk_b      	spk_b       	True
  794	spk_b       	spk_b      	spk_b       	True
  795	spk_b       	spk_b      	spk_b       	True
  796	spk_b       	spk_b      	spk_b       	True
  797	spk_b       	spk_b      	spk_b       	True
  798	spk_b       	spk_b      	spk_b       	True
  799	spk_b       	spk_b      	spk_b       	True
  800	spk_b       	spk_b      	spk_b       	True
  801	spk_b       	spk_b      	spk_b       	True
  802	spk_b       	spk_b      	spk_b       	True
  803	spk_b       	spk_b      	spk_b       	True
  804	spk_b       	spk_b      	spk_b       	True
  805	spk_b       	spk_b      	spk_b       	True
  806	spk_b       	spk_b      	spk_b       	True
  807	spk_b       	spk_b      	spk_b       	True
  808	spk_b       	spk_b      	spk_b       	True
  809	spk_b       	spk_b      	spk_b       	True
  810	spk_b       	spk_b      	spk_b       	True
  811	spk_b       	spk_b      	spk_b       	True
  812	spk_b       	spk_b      	spk_b       	True
  813	spk_b       	spk_b      	spk_b       	True
  814	spk_b       	spk_b      	spk_b       	True
  815	spk_b       	spk_b      	spk_b       	True
  816	spk_b       	spk_b      	spk_b       	True
  817	spk_b       	spk_b      	spk_b       	True
  818	spk_b       	spk_b      	spk_b       	True
  819	spk_b       	spk_b      	spk_b       	True
  820	spk_b       	spk_b      	spk_b       	True
  821	spk_b       	spk_b      	spk_b       	True
  822	spk_b       	spk_b      	spk_b       	True
  823	spk_b       	spk_b      	spk_b       	True
  824	spk_a       	spk_b      	spk_b       	False
  825	spk_a       	spk_b      	spk_b       	False
  826	spk_a       	spk_b      	spk_b       	False
  827	spk_a       	spk_b      	spk_b       	False
  828	spk_a       	spk_b      	spk_b       	False
  829	spk_a       	spk_b      	spk_b       	False
  830	spk_a       	spk_b      	spk_b       	False
  831	spk_a       	spk_b      	spk_b       	False
  832	spk_a       	spk_b      	spk_b       	False
  833	spk_a       	spk_b      	spk_b       	False
  834	spk_a       	spk_b      	spk_b       	False
  835	spk_a       	spk_b      	spk_b       	False
  836	spk_a       	spk_b      	spk_b       	False
  837	spk_a       	spk_b      	spk_b       	False
  838	spk_a       	spk_b      	spk_b       	False
  839	spk_a       	spk_b      	spk_b       	False
  840	spk_a       	spk_b      	spk_b       	False
  841	spk_a       	spk_b      	spk_b       	False
  842	spk_a       	spk_b      	spk_b       	False
  858	spk_b       	spk_b      	spk_b       	True
  859	spk_b       	spk_b      	spk_b       	True
  860	spk_b       	spk_b      	spk_b       	True
  861	spk_b       	spk_b      	spk_b       	True
  862	spk_b       	spk_b      	spk_b       	True
  863	spk_b       	spk_b      	spk_b       	True
  864	spk_b       	spk_b      	spk_b       	True
  865	spk_b       	spk_b      	spk_b       	True
  866	spk_b       	spk_b      	spk_b       	True
  867	spk_b       	spk_b      	spk_b       	True
  868	spk_b       	spk_b      	spk_b       	True
  869	spk_b       	spk_b      	spk_b       	True
  870	spk_b       	spk_b      	spk_b       	True
  871	spk_b       	spk_b      	spk_b       	True
  872	spk_b       	spk_b      	spk_b       	True
  873	spk_b       	spk_b      	spk_b       	True
  874	spk_b       	spk_b      	spk_b       	True
  875	spk_b       	spk_b      	spk_b       	True
  876	spk_b       	spk_b      	spk_b       	True
  877	spk_b       	spk_b      	spk_b       	True
  878	spk_b       	spk_b      	spk_b       	True
  879	spk_b       	spk_b      	spk_b       	True
  880	spk_b       	spk_b      	spk_b       	True
  881	spk_b       	spk_b      	spk_b       	True
  882	spk_b       	spk_b      	spk_b       	True
  883	spk_b       	spk_b      	spk_b       	True
  884	spk_b       	spk_b      	spk_b       	True
  885	spk_b       	spk_b      	spk_b       	True
  886	spk_b       	spk_b      	spk_b       	True
  887	spk_b       	spk_b      	spk_b       	True
  888	spk_b       	spk_b      	spk_b       	True
  889	spk_b       	spk_b      	spk_b       	True
  890	spk_b       	spk_b      	spk_b       	True
  891	spk_b       	spk_b      	spk_b       	True
  892	spk_b       	spk_b      	spk_b       	True
  893	spk_a       	spk_b      	spk_b       	False
  894	spk_a       	spk_b      	spk_b       	False
  895	spk_a       	spk_b      	spk_b       	False
  896	spk_b       	spk_b      	spk_b       	True
  897	spk_b       	spk_b      	spk_b       	True
  898	spk_b       	spk_b      	spk_b       	True
  899	spk_b       	spk_b      	spk_b       	True
  900	spk_b       	spk_b      	spk_b       	True
  901	spk_a       	spk_b      	spk_b       	False
  902	spk_a       	spk_b      	spk_b       	False
  903	spk_a       	spk_b      	spk_b       	False
  904	spk_a       	spk_b      	spk_b       	False
  905	spk_a       	spk_b      	spk_b       	False
  906	spk_a       	spk_b      	spk_b       	False
  907	spk_a       	spk_b      	spk_b       	False
  908	spk_a       	spk_b      	spk_b       	False
  909	spk_a       	spk_b      	spk_b       	False
  910	spk_a       	spk_b      	spk_b       	False
  911	spk_a       	spk_b      	spk_b       	False
  912	spk_b       	spk_b      	spk_b       	True
  913	spk_a       	spk_b      	spk_b       	False
  914	spk_a       	spk_b      	spk_b       	False
  915	spk_a       	spk_b      	spk_b       	False
  916	spk_b       	spk_b      	spk_b       	True
  917	spk_b       	spk_b      	spk_b       	True
  918	spk_b       	spk_b      	spk_b       	True
  919	spk_b       	spk_b      	spk_b       	True
  920	spk_b       	spk_b      	spk_b       	True
  921	spk_b       	spk_b      	spk_b       	True
  922	spk_b       	spk_b      	spk_b       	True
  923	spk_b       	spk_b      	spk_b       	True
  924	spk_b       	spk_b      	spk_b       	True
  925	spk_a       	spk_b      	spk_b       	False
  926	spk_a       	spk_b      	spk_b       	False
  927	spk_a       	spk_b      	spk_b       	False
  928	spk_b       	spk_b      	spk_b       	True
  929	spk_b       	spk_b      	spk_b       	True
  930	spk_b       	spk_b      	spk_b       	True
  931	spk_b       	spk_b      	spk_b       	True
  932	spk_b       	spk_b      	spk_b       	True
  933	spk_b       	spk_b      	spk_b       	True
  934	spk_b       	spk_b      	spk_b       	True
  935	spk_a       	spk_b      	spk_b       	False
  936	spk_a       	spk_b      	spk_b       	False
  937	spk_a       	spk_b      	spk_b       	False
  938	spk_a       	spk_b      	spk_b       	False
  939	spk_a       	spk_b      	spk_b       	False
  940	spk_a       	spk_b      	spk_b       	False
  941	spk_a       	spk_b      	spk_b       	False
  942	spk_a       	spk_b      	spk_b       	False
  943	spk_a       	spk_b      	spk_b       	False
  944	spk_a       	spk_b      	spk_b       	False
  945	spk_a       	spk_b      	spk_b       	False
  946	spk_a       	spk_b      	spk_b       	False
  947	spk_a       	spk_b      	spk_b       	False
  948	spk_a       	spk_b      	spk_b       	False
  949	spk_a       	spk_b      	spk_b       	False
  950	spk_a       	spk_b      	spk_b       	False
  951	spk_a       	spk_b      	spk_b       	False
  952	spk_b       	spk_b      	spk_b       	True
  953	spk_b       	spk_b      	spk_b       	True
  954	spk_b       	spk_b      	spk_b       	True
  955	spk_b       	spk_b      	spk_b       	True
  956	spk_b       	spk_b      	spk_b       	True
  957	spk_b       	spk_b      	spk_b       	True
  958	spk_b       	spk_b      	spk_b       	True
  959	spk_b       	spk_b      	spk_b       	True
  960	spk_b       	spk_b      	spk_b       	True
  961	spk_b       	spk_b      	spk_b       	True
  962	spk_b       	spk_b      	spk_b       	True
  978	spk_b       	spk_b      	spk_b       	True
  979	spk_b       	spk_b      	spk_b       	True
  980	spk_b       	spk_b      	spk_b       	True
  981	spk_b       	spk_b      	spk_b       	True
  982	spk_b       	spk_b      	spk_b       	True
  983	spk_b       	spk_b      	spk_b       	True
  984	spk_b       	spk_b      	spk_b       	True
  985	spk_b       	spk_b      	spk_b       	True
  986	spk_b       	spk_b      	spk_b       	True
  987	spk_b       	spk_b      	spk_b       	True
  988	spk_b       	spk_b      	spk_b       	True
  989	spk_b       	spk_b      	spk_b       	True
  990	spk_b       	spk_b      	spk_b       	True
  991	spk_b       	spk_b      	spk_b       	True
  992	spk_b       	spk_b      	spk_b       	True
  993	spk_b       	spk_b      	spk_b       	True
  994	spk_b       	spk_b      	spk_b       	True
  995	spk_b       	spk_b      	spk_b       	True
  996	spk_b       	spk_b      	spk_b       	True
  997	spk_b       	spk_b      	spk_b       	True
  998	spk_b       	spk_b      	spk_b       	True
  999	spk_b       	spk_b      	spk_b       	True
 1000	spk_b       	spk_b      	spk_b       	True
 1001	spk_b       	spk_b      	spk_b       	True
 1002	spk_b       	spk_b      	spk_b       	True
 1003	spk_b       	spk_b      	spk_b       	True
 1004	spk_b       	spk_b      	spk_b       	True
 1005	spk_b       	spk_b      	spk_b       	True
 1006	spk_b       	spk_b      	spk_b       	True
 1007	spk_b       	spk_b      	spk_b       	True
 1008	spk_b       	spk_b      	spk_b       	True
 1009	spk_b       	spk_b      	spk_b       	True
 1010	spk_b       	spk_b      	spk_b       	True
 1011	spk_b       	spk_b      	spk_b       	True
 1012	spk_b       	spk_b      	spk_b       	True
 1013	spk_b       	spk_b      	spk_b       	True
 1014	spk_b       	spk_b      	spk_b       	True
 1015	spk_b       	spk_b      	spk_b       	True
 1016	spk_b       	spk_b      	spk_b       	True
 1023	spk_b       	spk_b      	spk_b       	True
 1024	spk_b       	spk_b      	spk_b       	True
 1025	spk_b       	spk_b      	spk_b       	True
 1026	spk_b       	spk_b      	spk_b       	True
 1027	spk_b       	spk_b      	spk_b       	True
 1028	spk_b       	spk_b      	spk_b       	True
 1029	spk_b       	spk_b      	spk_b       	True
 1030	spk_b       	spk_b      	spk_b       	True
 1031	spk_b       	spk_b      	spk_b       	True
 1032	spk_b       	spk_b      	spk_b       	True
 1033	spk_b       	spk_b      	spk_b       	True
 1034	spk_b       	spk_b      	spk_b       	True
 1035	spk_b       	spk_b      	spk_b       	True
 1036	spk_b       	spk_b      	spk_b       	True
 1037	spk_b       	spk_b      	spk_b       	True
 1038	spk_b       	spk_b      	spk_b       	True
 1039	spk_b       	spk_b      	spk_b       	True
 1040	spk_b       	spk_b      	spk_b       	True
 1041	spk_b       	spk_b      	spk_b       	True
 1042	spk_b       	spk_b      	spk_b       	True
 1043	spk_b       	spk_b      	spk_b       	True
 1056	spk_b       	spk_b      	spk_b       	True
 1057	spk_b       	spk_b      	spk_b       	True
 1058	spk_a       	spk_b      	spk_b       	False
 1059	spk_a       	spk_b      	spk_b       	False
 1060	spk_a       	spk_b      	spk_b       	False
 1061	spk_b       	spk_b      	spk_b       	True
 1062	spk_b       	spk_b      	spk_b       	True
 1063	spk_b       	spk_b      	spk_b       	True
 1064	spk_b       	spk_b      	spk_b       	True
 1065	spk_b       	spk_b      	spk_b       	True
 1066	spk_b       	spk_b      	spk_b       	True
 1067	spk_b       	spk_b      	spk_b       	True
 1068	spk_b       	spk_b      	spk_b       	True
 1069	spk_b       	spk_b      	spk_b       	True
 1070	spk_b       	spk_b      	spk_b       	True
 1071	spk_b       	spk_b      	spk_b       	True
 1072	spk_b       	spk_b      	spk_b       	True
 1073	spk_b       	spk_b      	spk_b       	True
 1074	spk_b       	spk_b      	spk_b       	True
 1075	spk_b       	spk_b      	spk_b       	True
 1076	spk_b       	spk_b      	spk_b       	True
 1077	spk_b       	spk_b      	spk_b       	True
 1078	spk_a       	spk_b      	spk_b       	False
 1079	spk_a       	spk_b      	spk_b       	False
 1080	spk_a       	spk_b      	spk_b       	False
 1081	spk_a       	spk_b      	spk_b       	False
 1082	spk_b       	spk_b      	spk_b       	True
 1083	spk_a       	spk_b      	spk_b       	False
 1084	spk_a       	spk_b      	spk_b       	False
 1085	spk_a       	spk_b      	spk_b       	False
 1086	spk_a       	spk_b      	spk_b       	False
 1087	spk_a       	spk_b      	spk_b       	False
 1088	spk_a       	spk_b      	spk_b       	False
 1122	spk_a       	spk_b      	spk_b       	False
 1123	spk_a       	spk_b      	spk_b       	False
 1124	spk_a       	spk_b      	spk_b       	False
 1125	spk_a       	spk_b      	spk_b       	False
 1126	spk_a       	spk_b      	spk_b       	False
 1127	spk_b       	spk_b      	spk_b       	True
 1128	spk_b       	spk_b      	spk_b       	True
 1129	spk_b       	spk_b      	spk_b       	True
 1130	spk_a       	spk_b      	spk_b       	False
 1131	spk_a       	spk_b      	spk_b       	False
 1132	spk_a       	spk_b      	spk_b       	False
 1133	spk_a       	spk_b      	spk_b       	False
 1134	spk_a       	spk_b      	spk_b       	False
 1135	spk_a       	spk_b      	spk_b       	False
 1136	spk_a       	spk_b      	spk_b       	False
 1137	spk_a       	spk_b      	spk_b       	False
 1138	spk_a       	spk_b      	spk_b       	False
 1139	spk_a       	spk_b      	spk_b       	False
 1140	spk_a       	spk_b      	spk_b       	False
 1141	spk_a       	spk_b      	spk_b       	False
 1142	spk_a       	spk_b      	spk_b       	False
 1143	spk_a       	spk_b      	spk_b       	False
 1144	spk_a       	spk_b      	spk_b       	False
 1145	spk_a       	spk_b      	spk_b       	False
 1146	spk_a       	spk_b      	spk_b       	False
 1147	spk_a       	spk_b      	spk_b       	False
 1148	spk_a       	spk_b      	spk_b       	False
 1149	spk_a       	spk_b      	spk_b       	False
 1150	spk_a       	spk_b      	spk_b       	False
 1151	spk_a       	spk_b      	spk_b       	False
 1152	spk_a       	spk_b      	spk_b       	False
 1153	spk_a       	spk_b      	spk_b       	False
 1154	spk_a       	spk_b      	spk_b       	False
 1155	spk_a       	spk_b      	spk_b       	False
 1156	spk_a       	spk_b      	spk_b       	False
 1157	spk_a       	spk_b      	spk_b       	False
 1158	spk_a       	spk_b      	spk_b       	False
 1159	spk_a       	spk_b      	spk_b       	False
 1160	spk_a       	spk_b      	spk_b       	False
 1161	spk_a       	spk_b      	spk_b       	False
 1162	spk_a       	spk_b      	spk_b       	False
 1163	spk_b       	spk_b      	spk_b       	True
 1164	spk_b       	spk_b      	spk_b       	True
 1165	spk_b       	spk_b      	spk_b       	True
 1166	spk_b       	spk_b      	spk_b       	True
 1167	spk_b       	spk_b      	spk_b       	True
 1168	spk_b       	spk_b      	spk_b       	True
 1169	spk_a       	spk_b      	spk_b       	False
 1170	spk_a       	spk_b      	spk_b       	False
 1171	spk_a       	spk_b      	spk_b       	False
 1172	spk_a       	spk_b      	spk_b       	False
 1173	spk_a       	spk_b      	spk_b       	False
 1174	spk_a       	spk_b      	spk_b       	False
 1175	spk_a       	spk_b      	spk_b       	False
 1176	spk_a       	spk_b      	spk_b       	False
 1177	spk_a       	spk_b      	spk_b       	False
 1178	spk_a       	spk_b      	spk_b       	False
 1179	spk_b       	spk_b      	spk_b       	True
 1180	spk_b       	spk_b      	spk_b       	True
 1181	spk_b       	spk_b      	spk_b       	True
 1182	spk_b       	spk_b      	spk_b       	True
 1183	spk_b       	spk_b      	spk_b       	True
 1184	spk_b       	spk_b      	spk_b       	True
 1185	spk_b       	spk_b      	spk_b       	True
 1186	spk_b       	spk_b      	spk_b       	True
 1187	spk_b       	spk_b      	spk_b       	True

--- SUMMARY COUNTS ---
N_speech_diar_frames: 1007
N where mouth_winner == diar_winner: 516 (51.2%)
N where mouth_winner != diar_winner: 491 (48.8%)
Of those disagreements (491 frames):
  N where fused followed diar:  490 (99.8%)
  N where fused followed mouth: 0 (0.0%)

=== ITEM 2: STEP 0 ARTIFACTS ===

--- 2.1: output/audio_profile.json ---
{
  "file_path": "/Users/derekgagliano/Projects/podcast-editor/output/test_verify_manifest/temp_01_STRONGEST_The_Oral_Pill_Myth_PRESET_raw.wav",
  "sample_rate": 48000,
  "duration_seconds": 59.12,
  "channels": [
    {
      "channel_index": 0,
      "muffle": {
        "clarity_ratio": 0.0060117494757404785,
        "spectral_rolloff_hz": 2223.5423417842167,
        "spectral_centroid_hz": 1343.6293623222175
      },
      "noise": {
        "speech_rms": 0.028208202579233843,
        "noise_rms": 0.0005558030325944003,
        "noise_floor_db": -65.10158175875046,
        "snr_db": 34.10909003641998
      },
      "reverb": {
        "srmr": 4.780370634613393
      },
      "clipping": {
        "clipped_samples": 0,
        "near_clipped_samples": 0,
        "clipped_percentage": 0.0
      },
      "level": {
        "integrated_lufs": -31.520946301000183,
        "peak_db": -8.366443014679119,
        "true_peak_dbtp": -7.866443014679119,
        "dynamic_range_db": 56.735138744071335
      },
      "resonances": [
        {
          "frequency_hz": 486.3,
          "db_above_mean": 11.2,
          "q_estimate": 20.0
        },
        {
          "frequency_hz": 474.6,
          "db_above_mean": 7.8,
          "q_estimate": 20.0
        },
        {
          "frequency_hz": 416.0,
          "db_above_mean": 7.4,
          "q_estimate": 20.0
        }
      ],
      "recommended_highpass_hz": 80.0,
      "recommended_notches": [
        486.3,
        474.6,
        416.0
      ],
      "recommended_gain_db": 12.0
    }
  ],
  "cross_channel": {
    "channel_count": 1,
    "correlation_matrix": [
      [
        1.0
      ]
    ],
    "bleed_estimates_db": [
      [
        0.0
      ]
    ]
  },
  "recommendation": "LOCAL_NATURAL",
  "recommendation_reason": "Standard clean studio speech; optimal for adaptive highpass, with measured room notching, gentle compression, and BS.1770 loudnorm.",
  "analyzed_at": "2026-09-10T01:57:00.809563+00:00"
}

--- 2.1 (cont): DERIVED GATE/COMP/LIMITER VALUES & dsp.py DERIVATION LINES ---
=== DERIVATION LINES FROM reelcore/src/reelcore/audio/dsp.py ===
531:     gate_speech_offset = float(gate_cfg.get("gate_speech_offset_db", 4.0))
532:     gate_threshold_db = round(float(speech_median_db + gate_speech_offset), 1)
533: 
534:     gate_ratio_base = float(gate_cfg.get("gate_ratio_base", 2.4))
535:     gate_ratio_scale = float(gate_cfg.get("gate_ratio_snr_scale", 0.12))
536:     gate_ratio = round(float(gate_ratio_base + max(0.0, (measured_snr - 30.0) * gate_ratio_scale)), 1)
537: 
538:     gate_range_base = float(gate_cfg.get("gate_range_base_db", -12.0))
539:     gate_range_scale = float(gate_cfg.get("gate_range_snr_scale", 0.9))
540:     gate_range_min_db = float(gate_cfg.get("gate_range_min_db", -24.0))
541:     gate_range_db = round(float(np.clip(gate_range_base - (measured_snr - 30.0) * gate_range_scale, gate_range_min_db, 0.0)), 1)
542: 
543:     comp_depth_db = float(comp_cfg.get("comp_depth_db", -2.5))
544:     comp_threshold_db = round(float(speech_median_db - comp_depth_db), 1)
545: 
546:     limiter_ceiling = float(delivery_true_peak_max_dbtp)
547:     limiter_linear = float(lim_cfg.get("linear_limit", 0.78))
548: 

=== DERIVED VALUES FROM output/audio_profile.json ===
gate_threshold_db: -27.0
gate_ratio: 2.9
gate_range_db: -15.7
comp_threshold_db: -28.5
limiter_ceiling_dbtp: -1.0
limiter_linear: 0.78

--- 2.2: output/reel_1.qc.json (showing subject_center_x) ---
{
  "path": "output/PHASE_1_PRESET_REELS/01_STRONGEST_The_Oral_Pill_Myth_PRESET.mp4",
  "passed": true,
  "checks": [
    {
      "name": "resolution",
      "status": "PASS",
      "measured": "1080x1920",
      "expected": "1080x1920",
      "detail": ""
    },
    {
      "name": "encoder",
      "status": "PASS",
      "measured": "lavc62.28.101 libx264",
      "expected": "software encoder (libx264/libx265)",
      "detail": ""
    },
    {
      "name": "color metadata",
      "status": "PASS",
      "measured": "tagged",
      "expected": "color_space / transfer / primaries tagged",
      "detail": ""
    },
    {
      "name": "loudness (LUFS)",
      "status": "PASS",
      "measured": "-14.6 LUFS",
      "expected": "-14.0 +/- 1.0",
      "detail": ""
    },
    {
      "name": "true peak",
      "status": "PASS",
      "measured": "-1.8 dBTP",
      "expected": "<= -1.0 dBTP",
      "detail": ""
    },
    {
      "name": "loudness range",
      "status": "PASS",
      "measured": "8.4 LU",
      "expected": "<= 11.0 LU",
      "detail": ""
    },
    {
      "name": "speech vs room tone",
      "status": "PASS",
      "measured": "16.6 dB",
      "expected": ">= 15.0 dB",
      "detail": ""
    },
    {
      "name": "room resonance",
      "status": "WARN",
      "measured": "233 Hz +14.2 dB",
      "expected": "< +10 dB above local mean",
      "detail": "Notch the MEASURED frequency (233 Hz), not a guessed one. A notch at the wrong frequency thins the voice without removing the boxiness."
    },
    {
      "name": "head clipping (left)",
      "status": "PASS",
      "measured": "0% of frames",
      "expected": "<= 2%",
      "detail": ""
    },
    {
      "name": "head clipping (right)",
      "status": "PASS",
      "measured": "0% of frames",
      "expected": "<= 2%",
      "detail": ""
    },
    {
      "name": "subject centre X",
      "status": "PASS",
      "measured": "33.1% of width",
      "expected": "30%-70%",
      "detail": ""
    },
    {
      "name": "left/right balance",
      "status": "WARN",
      "measured": "90% left / 10% right",
      "expected": "neither half > 75%",
      "detail": "Most of the frame is background. On a vertical phone crop that is the most valuable space you have."
    },
    {
      "name": "headroom",
      "status": "PASS",
      "measured": "19.7% of height above head",
      "expected": "2%-20%",
      "detail": ""
    },
    {
      "name": "framing stability",
      "status": "WARN",
      "measured": "30px std",
      "expected": "< 25px",
      "detail": "The crop is moving. Quantize the path into holds and eased moves."
    }
  ],
  "measurements": {
    "resolution": "1080x1920",
    "duration_s": 59.1,
    "encoder": "lavc62.28.101 libx264",
    "audio_channels": 1,
    "sample_rate": 48000,
    "lufs": -14.6,
    "lra": 8.4,
    "true_peak": -1.8,
    "speech_to_noise_db": 16.6,
    "room_resonances_hz": [
      {
        "hz": 233,
        "db": 14.2
      },
      {
        "hz": 124,
        "db": 10.1
      },
      {
        "hz": 740,
        "db": 9.3
      },
      {
        "hz": 633,
        "db": 7.9
      },
      {
        "hz": 350,
        "db": 7.4
      }
    ],
    "framing_width": 1080,
    "framing_height": 1920,
    "framing_frames": 1773,
    "framing_detections": 60,
    "framing_edge_clip_fraction": 0.0,
    "framing_right_clip_fraction": 0.0,
    "framing_subject_center_x": 0.3305555555555555,
    "framing_head_top_fraction": 0.1967795138888889,
    "framing_left_half_share": 0.9010227997217465,
    "framing_center_x_std_px": 29.97290443050189
  }
}

--- 2.2 (cont): grep -n connectedComponentsWithStats reelcore/src/reelcore/qc.py ---
248:        num_labels, labels, stats, centroids = cv2.connectedComponentsWithStats(head, connectivity=8)

--- 2.3: FULL STDOUT OF ./venv/bin/python3 scripts/verify_phase2.py ---
objc[81101]: Class AVFFrameReceiver is implemented in both /Users/derekgagliano/Projects/podcast-editor/venv/lib/python3.11/site-packages/av/.dylibs/libavdevice.62.3.102.dylib (0x107d243a8) and /Users/derekgagliano/Projects/podcast-editor/venv/lib/python3.11/site-packages/cv2/.dylibs/libavdevice.61.3.100.dylib (0x1279883a8). This may cause spurious casting failures and mysterious crashes. One of the duplicates must be removed or renamed.
objc[81101]: Class AVFAudioReceiver is implemented in both /Users/derekgagliano/Projects/podcast-editor/venv/lib/python3.11/site-packages/av/.dylibs/libavdevice.62.3.102.dylib (0x107d243f8) and /Users/derekgagliano/Projects/podcast-editor/venv/lib/python3.11/site-packages/cv2/.dylibs/libavdevice.61.3.100.dylib (0x1279883f8). This may cause spurious casting failures and mysterious crashes. One of the duplicates must be removed or renamed.
/Users/derekgagliano/Projects/podcast-editor/venv/lib/python3.11/site-packages/torch/jit/_serialization.py:176: FutureWarning: `torch.jit.load` is deprecated. Please switch to `torch.export`.
  warnings.warn(
/Users/derekgagliano/Projects/podcast-editor/venv/lib/python3.11/site-packages/torch/jit/_serialization.py:176: FutureWarning: `torch.jit.load` is deprecated. Please switch to `torch.export`.
  warnings.warn(

===========================================================================
🧪 CHECK 1: Reel 1 re-renders from project.json to fresh path with QC diff
===========================================================================
✓ Manifest loaded: dallas1_master
✓ Source: input/raw_part_2_Dallas1.mov (3840x2160 @ 30.0fps)
✓ Target: 01_STRONGEST_The_Oral_Pill_Myth_PRESET (501.86s - 560.98s)
🎬 Executing real render from manifest to output/test_verify_manifest...
Loaded manifest for project: Dallas 1 Interview - Fenton Lebon & Dr. Saba Shabnam (dallas1_master)
Rendering target: 01_STRONGEST_The_Oral_Pill_Myth_PRESET (THE ORAL PILL MYTH)
Presets: delivery=social, framing=magnolia_interview, audio=magnolia_interview, captions=magnolia_clean
======================================================================
🎬 RENDERING REEL 1 VIA PRESETS: THE ORAL PILL MYTH
  • Delivery Preset : social (1080x1920 @ 30.0fps)
  • Audio Target    : -14.0 LUFS | TP <= -1.0 dBTP
  • Framing Preset  : magnolia_interview (Hold Tol: 40.0px)
  • Captions Preset : magnolia_clean (Georgia Bold / #FFE500)
  • Facing Direction: camera-right (Look room placement: left third ~35% X)
======================================================================
✓ Audio Preflight Complete:
    - Measured SNR: 34.1 dB
    - Measured Reverb (SRMR): 4.78
    - Adaptive Highpass: 80.0 Hz
    - Measured Resonances: [486.3, 474.6, 416.0]
✓ Dynamically Derived DSP Parameters:
    - Gate: thresh=-27.0 dB, ratio=2.9, range=-15.7 dB
    - Comp: thresh=-28.5 dB, ratio=2.8
    - Limiter: linear=0.780 (ceiling=-1.0 dBTP)
✓ DSP Chain Complete:
    - Speech/Room SNR: 16.6 dB (Target: >= 15 dB)
    - LRA            : 8.5 LU (Target: 5-8 LU)
    - LUFS           : -14.6 LUFS (Target: -14.0 +/- 1 LUFS)
    - True Peak      : -1.9 dBTP (Target: <= -1.0 dBTP)
🔍 Sampling real footage to compute face trajectory...
✓ Dynamic home_region: (14, 911, 608, 638)
✓ Real Framing Quantized: 1773 frames | 6 holds, 5 moves, 0 cuts | still 95.8% | max step 2.4px
    - Hold Fraction: 0.9577 (Target: >= 0.90)
✓ Caption Review JSON saved: 12 words flagged with confidence < 0.6
🎬 Rendering 1773 frames to output/test_verify_manifest/01_STRONGEST_The_Oral_Pill_Myth_PRESET.mp4...
✓ Master Render Complete: output/test_verify_manifest/01_STRONGEST_The_Oral_Pill_Myth_PRESET.mp4

🔍 EXECUTING QC GATE VERIFICATION...
QC REPORT  output/test_verify_manifest/01_STRONGEST_The_Oral_Pill_Myth_PRESET.mp4
==============================================================================
  PASS  resolution             1080x1920
  PASS  encoder                lavc62.28.101 libx264
  PASS  color metadata         tagged
  PASS  loudness (LUFS)        -14.6 LUFS
  PASS  true peak              -1.8 dBTP
  PASS  loudness range         8.4 LU
  PASS  speech vs room tone    16.6 dB
  WARN  room resonance         233 Hz +14.2 dB
                               expected: < +10 dB above local mean
                               -> Notch the MEASURED frequency (233 Hz), not a guessed one. A notch at the wrong frequency thins the voice without removing the boxiness.
  PASS  head clipping (left)   0% of frames
  PASS  head clipping (right)  0% of frames
  PASS  subject centre X       33.1% of width
  WARN  left/right balance     90% left / 10% right
                               expected: neither half > 75%
                               -> Most of the frame is background. On a vertical phone crop that is the most valuable space you have.
  PASS  headroom               19.7% of height above head
  WARN  framing stability      30px std
                               expected: < 25px
                               -> The crop is moving. Quantize the path into holds and eased moves.
==============================================================================
PASSED (14 checks, 0 failed, 3 warnings)

QC JSON saved to: output/test_verify_manifest/01_STRONGEST_The_Oral_Pill_Myth_PRESET.qc.json
✓ Render from manifest succeeded: output/test_verify_manifest/01_STRONGEST_The_Oral_Pill_Myth_PRESET.mp4
✓ QC Report: output/test_verify_manifest/01_STRONGEST_The_Oral_Pill_Myth_PRESET.qc.json

--------------------------------------------------------------------------------
Field                        | Reference    | Fresh Render | Tol    | Status
--------------------------------------------------------------------------------
lufs                         | -14.60       | -14.60       | 0.50   | PASS ✅
true_peak                    | -1.80        | -1.80        | 0.30   | PASS ✅
lra                          | 8.40         | 8.40         | 0.60   | PASS ✅
speech_to_noise_db           | 16.60        | 16.60        | 1.00   | PASS ✅
framing_subject_center_x     | 0.33         | 0.33         | 0.04   | PASS ✅
framing_head_top_fraction    | 0.20         | 0.20         | 0.04   | PASS ✅
--------------------------------------------------------------------------------
✅ CHECK 1 PASSED: Fresh render from project.json matches reference QC within tolerance!

===========================================================================
🧪 CHECK 2: Killing a job mid-caption and restarting skips real Whisper transcription
===========================================================================
Step 1: Running pipeline; Captions will crash mid-run...
[2026-09-10 00:46:20] [TRANSCRIBE] Starting TRANSCRIBE execution...
[2026-09-10 00:46:20] [TRANSCRIBE] Executing REAL faster-whisper transcription on 15s audio slice...
[2026-09-10 00:46:21] [TRANSCRIBE] Finished TRANSCRIBE in 0.86s
[2026-09-10 00:46:21] [BUILD_CAPTIONS] Starting BUILD_CAPTIONS execution...
[2026-09-10 00:46:21] [BUILD_CAPTIONS] Simulating process crash mid-caption burn (SIGKILL / Out of Memory)...
[2026-09-10 00:46:21] [BUILD_CAPTIONS] FAILED with error: Process killed mid-caption burn (SIGKILL / Out of Memory)
💥 Caught expected mid-job crash: Process killed mid-caption burn (SIGKILL / Out of Memory)

Step 2: Restarting pipeline after fixing caption handler...
⚡ [Job TRANSCRIBE] Reusing completed state from SQLite DB (test_real_resumability_TRANSCRIBE_6b91c209)
[2026-09-10 00:46:21] [BUILD_CAPTIONS] Starting BUILD_CAPTIONS execution...
[2026-09-10 00:46:21] [BUILD_CAPTIONS] Rendering kinetic subtitles from transcript...
[2026-09-10 00:46:21] [BUILD_CAPTIONS] Finished BUILD_CAPTIONS in 0.00s
Transcription call count after restart: 1 (must be 1)
✅ CHECK 2 PASSED: Restarting mid-caption successfully skipped real transcription!

===========================================================================
🧪 CHECK 3: Second run with no changes hits cache on every real analysis stage
===========================================================================
Pass 1 (Cold cache): Executing real analysis stages...
[2026-09-10 00:46:21] [ANALYZE_AUDIO] Starting ANALYZE_AUDIO execution...
[2026-09-10 00:46:21] [ANALYZE_AUDIO] Running REAL audio preflight on 15s audio slice...
[2026-09-10 00:46:22] [ANALYZE_AUDIO] Finished ANALYZE_AUDIO in 0.26s
[2026-09-10 00:46:22] [TRANSCRIBE] Starting TRANSCRIBE execution...
[2026-09-10 00:46:22] [TRANSCRIBE] Running REAL faster-whisper transcription on 15s slice...
[2026-09-10 00:46:23] [TRANSCRIBE] Finished TRANSCRIBE in 0.81s
Pass 1 real executions: {<JobType.ANALYZE_AUDIO: 'ANALYZE_AUDIO'>: 1, <JobType.TRANSCRIBE: 'TRANSCRIBE'>: 1}

Pass 2 (Warm cache): Re-running real analysis stages with unchanged inputs...
⚡ [Job ANALYZE_AUDIO] Reusing completed state from SQLite DB (test_real_caching_ANALYZE_AUDIO_a684dcea)
⚡ [Job TRANSCRIBE] Reusing completed state from SQLite DB (test_real_caching_TRANSCRIBE_08b669f6)
Pass 2 real executions: {<JobType.ANALYZE_AUDIO: 'ANALYZE_AUDIO'>: 1, <JobType.TRANSCRIBE: 'TRANSCRIBE'>: 1}
✅ CHECK 3 PASSED: Every single real analysis stage hit cache on the second run!

===========================================================================
🏁 PHASE 2 VERIFICATION SUMMARY
===========================================================================
1. Reel 1 from project.json:            PASSED ✅
2. Mid-caption failure skip transcribe:  PASSED ✅
3. Second run hits cache on all stages:  PASSED ✅
===========================================================================

--- 2.4: RE-TRANSCRIPTION RESULT FOR "BODY" & HAND-PATCH CONFIRMATION ---
=== GREP FOR BODY IN output/dallas1_transcript.json ===
Segment 8 [39.84 - 46.24] (speaker: spk_a):
  Text: Everybody's looking at NAD as a supplement, as a benefit for health and energy and longevity,
  Word: " Everybody's" (prob: 0.89710, start: 39.84, end: 40.42)

Segment 11 [51.64 - 58.52] (speaker: spk_a):
  Text: So NAD is a molecule that exists in every cell of your body, and NAD basically does two
  Word: " body," (prob: 0.99895, start: 56.08, end: 56.4)

Segment 17 [80.52 - 87.92] (speaker: spk_a):
  Text: So we need ATP as the central energy molecule to drive all energy reactions in our body.
  Word: " body." (prob: 0.92362, start: 87.66, end: 87.92)

Segment 27 [134.94 - 141.36] (speaker: spk_a):
  Text: Now inflammation is on the tip of everybody's tongue, and if you want to think about how
  Word: " everybody's" (prob: 0.98578, start: 136.78, end: 137.44)

Segment 39 [203.58 - 211.80] (speaker: spk_a):
  Text: called sirtuan, act in a cofactor way to stimulate the body to produce an enzyme called superoxide
  Word: " body" (prob: 0.99582, start: 208.56, end: 209.0)

Segment 171 [924.20 - 930.46] (speaker: spk_a):
  Text: doesn't work, and say you buy something because you hear somebody talk about it, and you think
  Word: " somebody" (prob: 0.92232, start: 929.12, end: 929.54)

Segment 191 [1050.22 - 1057.10] (speaker: spk_b):
  Text: and treatments every two weeks. She's been seeing everybody on our male hospital,
  Word: " everybody" (prob: 0.96208, start: 1053.76, end: 1054.94)

Segment 219 [1222.62 - 1227.66] (speaker: spk_b):
  Text: that is apparently causing all the disease process, and nobody has been able to evaluate
  Word: " nobody" (prob: 0.99277, start: 1225.78, end: 1226.02)

=== CONFIRMATION ===
Vocabulary initial_prompt loaded from: presets/captions/vocabulary.txt
Initial prompt terms: NAD, NAD+, NK cells, natural killer cells, CD38, PARP, mitochondria, ATP, Nano NAD, Fenton Lebon, Saba Shabnam, sub-Q, IV, peptides, SS-31, CVID, TBI
The word "body" was naturally transcribed by Whisper from audio without hand-patching (e.g. segment 11 prob 0.99895, segment 39 prob 0.99582).

--- 2.5: grep -rn "10.8\|35.1\|233\|frame\[900:1500" scripts/ ---
(Command returned exit code 1 with 0 matching lines; output is empty):

