# Frozen end-to-end study design

Prepared before the main runs on 2026-09-21. This study uses isolated deployments
of the exact repaired production images, existing Temporal, MongoDB, MySQL/MCP,
Vault and OPA services, and snapshot copies of production-labeled prompts. It
uses synthetic shopper registration/login and real gateway WebSockets. No orders,
HITL approvals or notifications are requested. Public browser SSO is outside scope.

Primary comparison: three tasks (exact plan quote, bundle details, three-turn
quote/compare/final-cart selection), two configurations (GPT-5.4 and GPT-5.4 nano),
four repetitions each: 24 attempted sessions. Within four repeat blocks, task and
configuration order is shuffled with seed 20260921. Both use the same API version,
prompts, tools, runtime evaluator and upsell chain. The model change applies to
every agent, including the inline evaluator and remote negotiation service.

Optimization: four additional three-turn sessions using GPT-5.4 with only the
duplicate transcript insertion in the cart extraction prompt removed. These are
interleaved with the comparison and paired by repetition with baseline multi-turn
sessions. Success must remain unchanged; token, cost and latency changes will be
reported even if the optimization fails or loses. Cache reads are observed, not
reset; report both actual cache-aware cost and an uncached repricing sensitivity.

SDK experiments, separately identified: two repetitions per model of the quote
task with (a) one synthetic HTTP 429 before forwarding the retried request to
Azure, and (b) synthetic HTTP 503 for all three SDK attempts of one logical call.
Eight additional sessions. These faults occur after sign-in, at the first model
request containing the task prompt. Synthetic rejected requests do not contact
the provider and have known zero model charge; their wait/latency is measured.
Any later genuine runtime work is included, even when the task fails. Natural
SDK retries are recorded independently by the same transport.

All HTTP attempts, provider-reported usage and responses are recorded without
credentials or authentication headers. x-stainless-retry-count identifies SDK
attempt numbers; a logical-call ID groups attempts. Langfuse traces provide agent,
tool and inline-evaluator attribution; Temporal history provides activity retries.
Record missing provider usage as unknown, never infer a zero provider charge.

Success: exact requested catalog base and activation prices; bundle speed, WiFi
and phone facts; final persisted cart contains the intended single product and
correct fees; the multi-turn comparison quotes both plans correctly. Register,
login and backend completion must succeed. A response fallback or completed
workflow alone is insufficient. Source WiFi 3-pack/2-node ambiguity is not scored
as a model failure. Rubric is deterministic plus output review, with no external
grading API calls; inline evaluation remains part of application runtime spend.

Cost per success is all runtime spend in the stated attempted-run cohort divided
by successes, including failed-attempt spending. Session totals include generated
sign-in work. Also provide task-only costs separately. Probe/pilot and injected
fault cohorts remain separate from the representative task mix. Startup, local
compute, infrastructure allocation, human labor, invoices and approval pauses are
not measured. Sample size is intentionally small; no population reliability claim.

Reference prices per million input/cached-input/output tokens: GPT-5.4
2.50/0.25/15 USD; nano 0.20/0.02/1.25 USD, to be checked against recorded model
pricing before final analysis. Per-process guard: 12 USD of known model usage;
batch guard: 20 USD. These are experiment guards, not assertions of invoice cost.
# Execution controls

The first deployment attempt exhausted cluster CPU reservations before any paid run. 
All experiment containers therefore reserve 20m CPU rather than production's 100m; CPU limits remain 500m. 
Runs are sequential. Shared-cluster scheduling remains a latency limitation.

All variants use an explicit 60-second SDK timeout and two SDK retries; production's implicit timeout may differ. 
Fulfillment and manager-notification activities are blocked in these isolated workers because the prescribed tasks authorize only quotes and cart changes. 
Any scope guard activation is retained and treated as failure. These controls apply equally to all variants.

Additional SDK microbenchmark, declared while the main batch was running and before
its own execution: two models × normal/withheld-response conditions × two repeats.
The prompt is exactly `Return exactly the word OK.` A successful first provider
response is captured for accounting but withheld from the SDK using a synthetic
ReadTimeout. The genuine SDK then retries against Azure. This isolates duplicated
generation spend; it is not a full Telecast task or a naturally occurring incident.
All eight logical calls and their actual provider requests are in a separate ledger.
