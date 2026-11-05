# Every attempted session

Full session costs include sign-in and inline evaluation. Null cache writes mean unreported. Missing costs are flagged, never assumed free. Counts are model logical calls / HTTP attempts / tools. Pilot rows are setup observations, excluded from the primary comparison.

| Run | Outcome | Model/HTTP/tool calls | Input/output | Cache read/write | SDK/Temporal retries | Task seconds | Runtime USD | Inline eval USD | Complete cost? |
|---|---|---|---|---|---|---:|---:|---:|---|
| comparison-cheaper-bundle_details-r1 | Pass | 24/24/10 | 89317/4374 | 48384/null | 0/0 | 92.68 | 0.01462178 | 0.00281835 | True |
| comparison-cheaper-bundle_details-r2 | Fail | 24/24/11 | 80533/4109 | 31488/null | 0/0 | 85.06 | 0.01557501 | 0.00246070 | True |
| comparison-cheaper-bundle_details-r3 | Pass | 22/22/8 | 86537/4208 | 45056/null | 0/0 | 74.75 | 0.01445732 | 0.00288480 | True |
| comparison-cheaper-bundle_details-r4 | Fail | 21/21/7 | 68267/3996 | 35072/null | 0/0 | 72.56 | 0.01233544 | 0.00267070 | True |
| comparison-cheaper-multi_turn-r1 | Pass | 32/32/9 | 65563/4758 | 22528/null | 0/0 | 100.84 | 0.01500506 | 0.00382310 | True |
| comparison-cheaper-multi_turn-r2 | Pass | 43/43/15 | 91480/6719 | 40960/null | 0/0 | 136.12 | 0.01932195 | 0.00516871 | True |
| comparison-cheaper-multi_turn-r3 | Pass | 27/27/8 | 55328/3814 | 15872/null | 0/0 | 76.96 | 0.01297614 | 0.00272443 | True |
| comparison-cheaper-multi_turn-r4 | Pass | 34/34/9 | 79652/5205 | 28672/null | 0/0 | 101.06 | 0.01727569 | 0.00490841 | True |
| comparison-cheaper-plan_quote-r1 | Pass | 21/21/7 | 40868/2626 | 19200/null | 0/0 | 61.79 | 0.00800010 | 0.00195095 | True |
| comparison-cheaper-plan_quote-r2 | Pass | 19/19/5 | 38965/2695 | 13824/null | 0/0 | 64.39 | 0.00867343 | 0.00227570 | True |
| comparison-cheaper-plan_quote-r3 | Pass | 19/19/5 | 37820/2580 | 14336/null | 0/0 | 48.28 | 0.00820852 | 0.00217520 | True |
| comparison-cheaper-plan_quote-r4 | Pass | 15/15/4 | 28237/1921 | 7680/null | 0/0 | 35.81 | 0.00666625 | 0.00143945 | True |
| comparison-current-bundle_details-r1 | Pass | 10/10/2 | 19210/1180 | 7168/null | 0/0 | 20.13 | 0.04959700 | 0.01029250 | True |
| comparison-current-bundle_details-r2 | Pass | 10/10/2 | 18986/1132 | 7296/null | 0/0 | 21.56 | 0.04802900 | 0.00994750 | True |
| comparison-current-bundle_details-r3 | Pass | 10/10/2 | 19185/1190 | 6144/null | 0/0 | 20.45 | 0.05198850 | 0.01022000 | True |
| comparison-current-bundle_details-r4 | Pass | 10/10/2 | 18912/1148 | 5120/null | 0/0 | 21.36 | 0.05298000 | 0.00981500 | True |
| comparison-current-multi_turn-r1 | Pass | 22/22/4 | 45134/2546 | 7680/null | 0/0 | 58.39 | 0.13374500 | 0.03687500 | True |
| comparison-current-multi_turn-r2 | Pass | 22/22/4 | 45103/2681 | 9984/null | 0/0 | 56.43 | 0.13050850 | 0.03680000 | True |
| comparison-current-multi_turn-r3 | Pass | 30/30/6 | 63840/3784 | 19840/null | 0/0 | 85.23 | 0.17172000 | 0.05448000 | True |
| comparison-current-multi_turn-r4 | Pass | 22/22/4 | 45100/2566 | 10240/null | 0/0 | 58.51 | 0.12820000 | 0.03674250 | True |
| comparison-current-plan_quote-r1 | Pass | 9/9/1 | 14908/868 | 3072/null | 0/0 | 15.63 | 0.04337800 | 0.00925000 | True |
| comparison-current-plan_quote-r2 | Pass | 13/13/2 | 24211/1366 | 11392/null | 0/0 | 26.16 | 0.05538550 | 0.01211000 | True |
| comparison-current-plan_quote-r3 | Pass | 9/9/1 | 14828/818 | 3072/null | 0/0 | 15.37 | 0.04242800 | 0.00909250 | True |
| comparison-current-plan_quote-r4 | Pass | 13/13/2 | 23872/1386 | 8704/null | 0/0 | 28.30 | 0.06088600 | 0.01628500 | True |
| optimization-optimized-multi_turn-r1 | Pass | 30/30/8 | 65277/3894 | 29312/null | 0/0 | 82.52 | 0.15565050 | 0.04523000 | True |
| optimization-optimized-multi_turn-r2 | Pass | 22/22/4 | 42748/2457 | 8320/null | 0/0 | 64.83 | 0.12500500 | 0.03645250 | True |
| optimization-optimized-multi_turn-r3 | Pass | 19/19/3 | 37163/2072 | 10368/null | 0/0 | 45.25 | 0.10065950 | 0.02680750 | True |
| optimization-optimized-multi_turn-r4 | Pass | 30/30/7 | 63397/3726 | 22400/null | 0/0 | 78.21 | 0.16398250 | 0.04721700 | True |
| pilot-cheaper-plan_quote-r0 | Fail | 0/0/0 | 0/0 | 0/null | 0/0 | 0.00 | 0.00000000 | 0.00000000 | True |
| pilot-cheaper-plan_quote-r1 | Fail | 22/22/8 | 41602/2991 | 12544/null | 0/0 | 60.81 | 0.00980123 | 0.00200945 | True |
| pilot-current-plan_quote-r0 | Pass | 14/9/2 | 23975/1319 | 4992/null | None/1 | 148.01 | 0.06849050 | 0.01621750 | False |
| pilot-current-plan_quote-r1 | Pass | 13/13/2 | 24194/1390 | 6784/null | 0/0 | 26.36 | 0.06607100 | 0.01639250 | True |
| pilot-optimized-plan_quote-r0 | Fail | 0/0/0 | 0/0 | 0/null | 0/0 | 0.00 | 0.00000000 | 0.00000000 | True |
| pilot-optimized-plan_quote-r1 | Pass | 9/9/1 | 14556/826 | 3200/null | 0/0 | 17.40 | 0.04158000 | 0.00913250 | True |
| sdk_fault-cheaper-exhaust503-r1 | Pass | 21/23/4 | 38624/3041 | 13056/null | 2/0 | 57.21 | 0.00917597 | 0.00301365 | True |
| sdk_fault-cheaper-exhaust503-r2 | Fail | 13/15/2 | 20257/1425 | 4608/null | 2/0 | 29.00 | 0.00500321 | 0.00129470 | True |
| sdk_fault-cheaper-retry429-r1 | Pass | 22/23/8 | 44706/3015 | 19968/null | 1/0 | 60.68 | 0.00911571 | 0.00227585 | True |
| sdk_fault-cheaper-retry429-r2 | Pass | 19/20/5 | 38074/2643 | 16640/null | 1/0 | 56.45 | 0.00792335 | 0.00192319 | True |
| sdk_fault-current-exhaust503-r1 | Pass | 12/14/1 | 17733/1136 | 5248/null | 2/0 | 25.91 | 0.04956450 | 0.01361850 | True |
| sdk_fault-current-exhaust503-r2 | Pass | 12/14/1 | 17732/1142 | 3200/null | 2/0 | 26.32 | 0.05426000 | 0.01618500 | True |
| sdk_fault-current-retry429-r1 | Pass | 9/10/1 | 14856/866 | 3072/null | 1/0 | 19.08 | 0.04321800 | 0.00913250 | True |
| sdk_fault-current-retry429-r2 | Pass | 17/18/3 | 33571/1939 | 14464/null | 1/0 | 39.22 | 0.08046850 | 0.02406250 | True |
