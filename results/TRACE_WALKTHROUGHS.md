# Two execution walkthroughs

Selected mechanically: a successful GPT-5.4 run nearest that cohort's median session cost, and the highest-cost failure across normal and SDK-fault cohorts (or highest-cost run if no failures). All costs are calculated reference model costs.

## Typical: comparison-current-plan_quote-r2

Outcome: pass. Session $0.05538550; task $0.04668200; first task logical call $0.00258550; full-task/first-call 18.06×. Sign-in model spend $0.00870350. Task latency 26.16s; inline evaluation $0.01211000 included. Checks: `{"transport": true, "authenticated_identity": true, "quote_300": true, "persisted_cart": true, "no_scope_guard": true}`.

| Step | Phase / agent | HTTP / retry | Input / cached / output | Call USD | Cumulative session USD |
|---:|---|---|---|---:|---:|
| 1 | authentication_setup / coordinator_agent.run | 200 / 0 | 1112 / 1024 / 88 | 0.00179600 | 0.00179600 |
| 2 | authentication_setup / shopping_cart_agent.run | 200 / 0 | 813 / 0 / 87 | 0.00333750 | 0.00513350 |
| 3 | authentication_setup / eval_agent.run | 200 / 0 | 1332 / 0 / 16 | 0.00357000 | 0.00870350 |
| 4 | task_turn_1 / coordinator_agent.run | 200 / 0 | 1267 / 1152 / 134 | 0.00258550 | 0.01128900 |
| 5 | task_turn_1 / catalog_agent.run | 200 / 0 | 1980 / 1152 / 84 | 0.00361800 | 0.01490700 |
| 6 | task_turn_1 / catalog_agent.run | 200 / 0 | 3971 / 1152 / 109 | 0.00897050 | 0.02387750 |
| 7 | task_turn_1 / shopping_cart_agent.run | 200 / 0 | 1196 / 0 / 178 | 0.00566000 | 0.02953750 |
| 8 | task_turn_1 / eval_agent.run | 200 / 0 | 2128 / 0 / 132 | 0.00730000 | 0.03683750 |
| 9 | task_turn_1 / catalog_agent.run | 200 / 0 | 1980 / 1152 / 107 | 0.00396300 | 0.04080050 |
| 10 | task_turn_1 / catalog_agent.run | 200 / 0 | 3971 / 3840 / 110 | 0.00293750 | 0.04373800 |
| 11 | task_turn_1 / shopping_cart_agent.run | 200 / 0 | 1251 / 0 / 167 | 0.00563250 | 0.04937050 |
| 12 | task_turn_1 / eval_agent.run | 200 / 0 | 2128 / 1920 / 16 | 0.00124000 | 0.05061050 |
| 13 | task_turn_1 / upsell_agent.run | 200 / 0 | 1082 / 0 / 138 | 0.00477500 | 0.05538550 |

The task traversed 10 HTTP attempts across 8 agent invocations. It included 3 inline evaluator invocations across sign-in and task, 0 SDK retries, and 0 Temporal activity retries. These are different kinds of repetition; evaluator-directed regeneration can add calls even with zero SDK retries.

The coordinator routed to the catalog, which fetched the plan prices. Cart extraction then added a SHOPPING_CART block. The first task evaluator objected to its tax/grand-total fields and requested a quote without that block. The application called the catalog and cart agents again, then the evaluator passed the result. Steps 9–12 added $0.01377300, including the second task evaluation, before the final upsell call. This rework is already included in the totals above.

Cart extraction input went from 1196 tokens in its first task invocation to 1251 in its last. The cart and evaluator consume accumulated conversation/state, while other agents have their own prompt shapes: context is not one monotonically growing global window.

Within catalog_agent.run, consecutive inputs changed from 1980 to 3971 tokens. Calculated call cost changed from $0.00361800 to $0.00897050, with 1152 versus 1152 cached tokens. Tool results, output length and caching all affect what context growth costs on the actual path.

Raw path: session records for comparison-current-plan_quote-r2 (gateway events, persisted state, agent and tool traces) are retained but not published.

## Expensive or failed: comparison-cheaper-bundle_details-r2

Outcome: fail. Session $0.01557501; task $0.01485738; first task logical call $0.00040430; full-task/first-call 36.75×. Sign-in model spend $0.00071763. Task latency 85.06s; inline evaluation $0.00246070 included. Checks: `{"transport": true, "authenticated_identity": true, "base_monthly": true, "activation_fee": true, "speed": true, "wifi": true, "phone": true, "persisted_cart": false, "no_scope_guard": true}`.

| Step | Phase / agent | HTTP / retry | Input / cached / output | Call USD | Cumulative session USD |
|---:|---|---|---|---:|---:|
| 1 | authentication_setup / coordinator_agent.run | 200 / 0 | 1112 / 1024 / 81 | 0.00013933 | 0.00013933 |
| 2 | authentication_setup / shopping_cart_agent.run | 200 / 0 | 808 / 0 / 98 | 0.00028410 | 0.00042343 |
| 3 | authentication_setup / eval_agent.run | 200 / 0 | 1321 / 0 / 24 | 0.00029420 | 0.00071763 |
| 4 | task_turn_1 / coordinator_agent.run | 200 / 0 | 1284 / 0 / 118 | 0.00040430 | 0.00112193 |
| 5 | task_turn_1 / catalog_agent.run | 200 / 0 | 1997 / 1024 / 150 | 0.00040258 | 0.00152451 |
| 6 | task_turn_1 / catalog_agent.run | 200 / 0 | 2169 / 1280 / 166 | 0.00041090 | 0.00193541 |
| 7 | task_turn_1 / catalog_agent.run | 200 / 0 | 8733 / 1280 / 147 | 0.00169995 | 0.00363536 |
| 8 | task_turn_1 / catalog_agent.run | 200 / 0 | 9183 / 7936 / 270 | 0.00074562 | 0.00438098 |
| 9 | task_turn_1 / shopping_cart_agent.run | 200 / 0 | 1456 / 0 / 115 | 0.00043495 | 0.00481593 |
| 10 | task_turn_1 / eval_agent.run | 200 / 0 | 2398 / 0 / 209 | 0.00074085 | 0.00555678 |
| 11 | task_turn_1 / catalog_agent.run | 200 / 0 | 1997 / 1792 / 144 | 0.00025684 | 0.00581362 |
| 12 | task_turn_1 / catalog_agent.run | 200 / 0 | 2446 / 1280 / 177 | 0.00048005 | 0.00629367 |
| 13 | task_turn_1 / catalog_agent.run | 200 / 0 | 2896 / 1536 / 378 | 0.00077522 | 0.00706889 |
| 14 | task_turn_1 / shopping_cart_agent.run | 200 / 0 | 1619 / 0 / 144 | 0.00050380 | 0.00757269 |
| 15 | task_turn_1 / eval_agent.run | 200 / 0 | 2570 / 0 / 151 | 0.00070275 | 0.00827544 |
| 16 | task_turn_1 / catalog_agent.run | 200 / 0 | 1997 / 1792 / 159 | 0.00027559 | 0.00855103 |
| 17 | task_turn_1 / catalog_agent.run | 200 / 0 | 10232 / 1024 / 168 | 0.00207208 | 0.01062311 |
| 18 | task_turn_1 / catalog_agent.run | 200 / 0 | 10682 / 9472 / 277 | 0.00077769 | 0.01140080 |
| 19 | task_turn_1 / shopping_cart_agent.run | 200 / 0 | 1589 / 0 / 164 | 0.00052280 | 0.01192360 |
| 20 | task_turn_1 / eval_agent.run | 200 / 0 | 2402 / 0 / 194 | 0.00072290 | 0.01264650 |
| 21 | task_turn_1 / upsell_agent.run | 200 / 0 | 1092 / 0 / 102 | 0.00034590 | 0.01299240 |
| 22 | task_turn_1 / upsell_agent.run | 200 / 0 | 1264 / 0 / 218 | 0.00052530 | 0.01351770 |
| 23 | task_turn_1 / upsell_agent.run | 200 / 0 | 1361 / 1024 / 258 | 0.00041038 | 0.01392808 |
| 24 | task_turn_1 / upsell_agent.run | 200 / 0 | 7925 / 1024 / 197 | 0.00164693 | 0.01557501 |

The task traversed 21 HTTP attempts across 11 agent invocations. It included 4 inline evaluator invocations across sign-in and task, 0 SDK retries, and 0 Temporal activity retries. These are different kinds of repetition; evaluator-directed regeneration can add calls even with zero SDK retries.

The first catalog pass fetched bundle facts, but the evaluator rejected their grounding. The revised answer omitted the activation fee, prompting another blocking verdict and another catalog pass. The third evaluator still challenged grounding even though the final facts matched the recorded MCP catalog outputs. Upsell then made four additional model calls. The final customer-facing quote was factually correct, but MongoDB retained an empty cart: this fails the predefined application-state rubric and passes the answer-only sensitivity criterion. The inline evaluator's verdict is not treated as benchmark ground truth.

Cart extraction input went from 1456 tokens in its first task invocation to 1589 in its last. The cart and evaluator consume accumulated conversation/state, while other agents have their own prompt shapes: context is not one monotonically growing global window.

Within catalog_agent.run, consecutive inputs changed from 1997 to 10232 tokens. Calculated call cost changed from $0.00027559 to $0.00207208, with 1792 versus 1024 cached tokens. Tool results, output length and caching all affect what context growth costs on the actual path.

Raw path: comparison-cheaper-bundle_details-r2 is retained but not published.

