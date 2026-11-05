# From Tokenomics to Taskonomics — completed evidence

**The whole execution path is the useful cost unit.** Across 24 controlled EKS
sessions, task model spend was **7.05–49.98× the first task call**
(median **21.74×**). 12/24 full sessions cost more than two cents.
These are calculated USD model costs from measured provider usage and reference
prices, not invoices or total infrastructure/labor cost. Sign-in and inline
evaluation are included in full-session costs; first-call ratios compare task
phases only. [Machine-readable facts](results/article_facts.json).

The new study contains **24 comparison sessions** (3 tasks × 2 configurations ×
4 repeats), **4 additional optimization sessions**, **8 injected-fault sessions**,
and **6 separately retained pilot attempts**. Two pilots failed locally before
backend session creation. A separate **8-call SDK microbenchmark** tests response
loss. No samples are removed because their answers or costs are unfavorable.

**Model comparison.** All-attempt spend divided by successful runs includes failed
attempts in the numerator. Benchmark-only grading is excluded; its API cost is
zero in this study. A successful task requires correct requested catalog facts,
successful authentication/transport, and the intended final single-item cart with
correct fees. Answer-only success removes the cart requirement as a sensitivity
check. This matters because a correct quote can coexist with an empty cart.
For quote-only prompts, the cart requirement is an application acceptance criterion,
not an explicit customer instruction to purchase; the answer-only column prevents
that stricter rubric from being mistaken for a factual-answer failure.

| Configuration | Successes | Answer-only successes | Mean model calls | Median task seconds | All-attempt runtime USD | Failed-attempt USD | USD per success |
|---|---:|---:|---:|---:|---:|---:|---:|
| GPT-5.4 | 12/12 | 12/12 | 15.00 | 23.86 | 0.96884550 | 0.00000000 | $0.080737 |
| GPT-5.4 nano | 10/12 | 11/12 | 25.08 | 75.85 | 0.15311669 | 0.02791045 | $0.015312 |

Using the answer-only sensitivity criterion, cost per success is
$0.080737 for GPT-5.4 and
$0.013920 for nano.
The two nano comparison failures both left an empty bundle cart; one also quoted
$139.99 as the base price instead of the catalog's $149.99. The other answer was
factually correct. These failures remain in the spend numerator.

| Task | Configuration | Successes | Model-call range | Task latency range, seconds | Session cost range, USD |
|---|---|---:|---:|---:|---:|
| plan_quote | current | 4/4 | 9–13 | 15.37–28.30 | 0.042428–0.060886 |
| plan_quote | cheaper | 4/4 | 15–21 | 35.81–64.39 | 0.006666–0.008673 |
| bundle_details | current | 4/4 | 10–10 | 20.13–21.56 | 0.048029–0.052980 |
| bundle_details | cheaper | 2/4 | 21–24 | 72.56–92.68 | 0.012335–0.015575 |
| multi_turn | current | 4/4 | 22–30 | 56.43–85.23 | 0.128200–0.171720 |
| multi_turn | cheaper | 4/4 | 27–43 | 76.96–136.12 | 0.012976–0.019322 |

The current deployment is GPT-5.4 (`gpt-5.4-2026-03-05`); the cheaper configuration
is GPT-5.4 nano (`gpt-5.4-nano-2026-03-17`). The change applies to every agent,
including the inline evaluator. Actual requests omit temperature, reasoning effort
and output-token caps; both use the provider defaults, nonstreaming requests,
API version `2025-04-01-preview`, SDK `max_retries=2`, and a 60-second timeout.
Prompt snapshots and underlying tools are identical. [Effective requests](results/effective_settings.json)
and [image digests](data/isolation.json) are published; the deployed version and
source snapshot is retained but not published.

**What the evaluator costs.** Inline evaluation accounted for
**$0.28721050 of $1.12196219 (25.6%)** in the comparison.
This is application runtime, not benchmark grading. Evaluator-triggered rework is
also visible in the agent traces and remains in runtime spend. The older local
study separately spent **$0.01798193 on 44 benchmark grading calls**; that historical
expense is not added to this experiment's runtime numerator.
An inline evaluator verdict is not benchmark ground truth: the failed bundle
walkthrough includes objections to facts confirmed in the actual catalog tool
outputs. The benchmark checks the answer and persisted state independently.

**Context and caching.** current: 28.2% of input cached, uncached repricing $0.099433 per success; cheaper: 42.4% of input cached, uncached repricing $0.021127 per success.
Cache reads are provider-reported; cache writes are unreported (`null`). Cache state
was observed rather than flushed or equalized. Uncached repricing applies different
rates to the same observed usage/path; it is a calculation, not another experiment.
Growing input generally raises uncached cost, but cache hits, output length and
changes in the execution path prevent a simple monotonic cost story. No context
pricing tier transition was encountered.

**Optimization test.** Four GPT-5.4 three-turn sessions removed the duplicate
conversation insertion from the cart extraction prompt; full conversation messages
remained available. Compared with four interleaved baseline repeats, successes were
**4/4 → 4/4**. The measured component
improvement was cart-extraction input **30340 → 23490
(-22.6%)** and cart model cost
**$0.12800500 → $0.11533500
(-9.9%)**, with zero cache reads in both.
That covers 18 versus 19 cart calls during tasks.

Across the **whole session**, successes remained
**4/4 → 4/4**, session input tokens
**199177 → 208585 (+4.7%)**,
and all-attempt model spend **$0.56417350 → $0.54529750
(-3.3%)**. Uncached repricing changes by
+4.8%. The end-to-end cost reduction is measured, but it combines changed token volume, output/rework and uncontrolled cache state.
Mean model calls were 24.00 → 25.25;
median task latency was 58.45s → 71.52s;
cached input tokens were 47744 → 70400.
Only 2/4 matched repeat blocks had lower optimized cost; the aggregate
change should not be presented as a reliable population saving from this small sample.
The earlier local fixture experiment measured 21.8% lower cost and 4/4 success in
both conditions; that narrower result remains separately labeled and is not a
production saving claim. [Paired chart](results/optimization.png).

**SDK retries are measurable.** The transport records each attempt beneath the
LangChain generation, including retry index, timing, status and provider usage.
Normal comparison sessions had 0 SDK retries and
0 Temporal activity retries. Controlled 429/503
responses test recovery and exhaustion in full application sessions:

| Injected condition | Configuration | Successes | HTTP attempts | SDK retries | All-attempt runtime USD |
|---|---|---:|---:|---:|---:|
| retry429 | current | 2/2 | 28 | 2 | 0.12368650 |
| retry429 | cheaper | 2/2 | 43 | 2 | 0.01703906 |
| exhaust503 | current | 2/2 | 28 | 4 | 0.10382450 |
| exhaust503 | cheaper | 1/2 | 38 | 4 | 0.01417918 |

Injected rejected attempts never reached Azure and have known zero provider model
charge. Other real calls in those failed/successful sessions are still included.
3/4 retry-exhaustion tasks still passed final checks: application-level
recovery continued after the first SDK call failed.
The separate response-loss microbenchmark captured successful generation, withheld
the response from the SDK, then observed a genuine retry against Azure. Across
four such cases, **8 provider requests occurred for
four SDK calls**, and **$0.00019480 of extra generated output/input cost** was visible
only below the SDK's final returned usage. All eight microbenchmark calls cost
$0.00058440, kept outside representative-task runtime totals.
[Raw retry ledger](results/sdk_response_loss.csv). This demonstrates a mechanism;
it does not measure a natural outage rate or Azure billing for actual lost responses.
A genuine response lost before the transport receives it can leave token usage
unknown; provider-side records would then be needed to reconcile its cost.

**Two actual paths.** [Trace walkthroughs](results/TRACE_WALKTHROUGHS.md) show the
median-near successful current-model run and the highest-cost failed run (or most
expensive run if no failure), with per-call input/cache/output, cumulative cost,
agent transitions and first-call/full-task comparison.

**Claims assessment.** Whole-task accounting is supported. Context growth adds
billable input, while caching can offset its rate; both are visible, but uncontrolled
cache state prevents a causal cache-hit performance claim. SDK retry spend depends
on whether generation already happened; rejection/backoff is not automatically a
second generation charge. Inline and benchmark evaluation must be separated.
The cheaper-model results above determine the conclusion: extra steps do not by
themselves prove higher cost per success. Approval-pause economics remain untested;
these tasks request no approval, order or outbound notification.

**Limits and reproduction.** These are controlled synthetic-customer experiments on
isolated copies of the production images using real EKS/Temporal/MongoDB/MySQL/MCP.
The production OPA/Vault configuration is retained; negotiation, approvals and
fulfillment are outside the tested paths. These are not natural production traffic or a public
browser SSO test. The gateway's real register/login and WebSocket auth update are
exercised through an AWS-authenticated tunnel. Only quote/catalog/cart tasks are
covered. Four repeats per cell expose variability but do not establish population
reliability. Workers reserve 20m CPU versus production's 100m, retain 500m CPU
limits, and run tasks sequentially on a shared cluster. Timeout is explicitly 60s;
production's implicit setting may differ. Fulfillment/manager-message activities
are blocked equally across variants. Azure subscription pricing/discounts,
infrastructure allocation, human labor and cache writes remain unverified.
Tool counts are logical invocation spans; underlying MCP transport retries are not
separately counted. Local study orchestration and analysis compute are not allocated.

Every main-session token total is reconciled against independent Langfuse provider
usage in [accounting verification](results/accounting_verification.json). One early
pilot crossed a worker transition: five missing HTTP-generation records were
recovered from Langfuse usage, but its physical retry coverage remains incomplete;
it is excluded from comparison and marked accordingly. All artifacts, failures,
settings, success checks and [exact reproduction commands](REPRODUCE.md) are retained.
Published here: [all attempted runs](results/ALL_RUNS.md), the
[per-run CSV](results/runs.csv), [pricing](pricing.json) and the
[frozen design](DESIGN.md). The per-attempt HTTP ledger and the catalog ground
truth are retained but not published.

**Five takeaways**

1. Price the complete path to a checked outcome; full-task spend was a median 21.74× the first call here.
2. Count failures in cost per success, and state whether success includes persisted application state.
3. Count runtime evaluators as part of the application: they consumed 25.6% of comparison model spend before attributing any induced rework.
4. Measure below the SDK: a returned generation can hide another completed provider generation after response loss.
5. Validate optimizations on complete tasks; per-call token savings and cheaper models do not alone predict end-to-end cost or latency.
