# Cost per completed task — study records

Supporting evidence for [From Tokenomics to Taskonomics](https://medium.com/@parthamadhira/from-tokenomics-to-taskonomics-89abc5a9c5b9).

A multi-agent application was measured across 24 controlled backend sessions in
September 2026, comparing two model configurations on three tasks. This folder
holds the design, the per-run measurements, and the response-loss ledger, so the
figures quoted in the article can be checked independently.

## What is here

| File | What it is |
|---|---|
| `DESIGN.md` | The study design, frozen before the runs on 2026-09-21. Acceptance criteria, cohorts, cost boundary and guards were fixed in advance. |
| `FINDINGS.md` | Full results narrative, including limits and claims that are *not* supported. |
| `results/comparison.csv` | Cohort-level totals: attempts, successes, spend, cost per success, median latency. |
| `results/runs.csv` | Per-session measurements for all 46 attempted runs across every cohort. |
| `results/sdk_response_loss.csv` | The eight-call SDK microbenchmark: HTTP attempts, SDK retries, all-provider cost, and the cost visible in the SDK's returned usage. |

## Checking the article's numbers

Every figure in the article resolves to a file here.

| Article claim | Where to check |
|---|---|
| 12/12 and 10/12 sessions passing acceptance | `results/comparison.csv`, columns `attempts` / `successes` |
| 96.88c and 15.31c total spend across all attempts | `results/comparison.csv`, `runtime_cost_usd` |
| 8.07c and 1.53c per successful session | `results/comparison.csv`, `cost_per_success_usd` |
| 2.79c spent on the two failed nano sessions | `results/comparison.csv`, `failed_run_spend_usd` |
| 1.39c per success under the answer-only bar | `results/runs.csv`, `answer_only_success` column (11 of 12 for nano) |
| Nano cheaper in each of the four repeat groups | `results/runs.csv` — see the snippet below |
| Evaluator was 25.6% of comparison spend | `results/comparison.csv`, `runtime_inline_eval_cost_usd` summed across both comparison rows, over total comparison spend |
| No natural SDK retries in the 24 sessions | `results/runs.csv`, `sdk_retries` is 0 for every `comparison` row |
| Four injected cases produced eight provider requests | `results/sdk_response_loss.csv`, `http_attempts` and `all_provider_cost_usd` against `sdk_visible_last_response_cost_usd` |

To reproduce the repeat-group result:

```python
import csv, collections
rows = [r for r in csv.DictReader(open('results/runs.csv'))
        if r['cohort'] == 'comparison']
by = collections.defaultdict(lambda: collections.defaultdict(float))
for r in rows:
    by[r['repeat']][r['variant']] += float(r['runtime_cost_usd'])
for k in sorted(by):
    print(k, dict(by[k]))
```

Nano is cheaper in all four repeat groups, and in all twelve task-by-repeat
pairings individually. The two variants' totals sum to 0.96884550 and
0.15311669 USD respectively.

## What is not here, and why

The full request and response traces, the Temporal activity logs, the inline
evaluator outputs and the catalog ground truth are retained but not published.
They contain prompt text and application internals from an isolated copy of a
production system. Everything needed to verify the article's arithmetic is in
this folder; the raw traces would only be needed to re-run the study.

Costs are calculated from measured provider usage and the reference prices
recorded in `DESIGN.md`. They are not reconciled against invoices, and they
exclude infrastructure and human effort.

## Limits

Twelve sessions per configuration do not establish production reliability. These
were controlled backend sessions on isolated deployments, not natural customer
traffic. The response-loss microbenchmark demonstrates an accounting mechanism;
it does not measure how often that mechanism fires in production, and no natural
SDK retries occurred in the main comparison. `FINDINGS.md` states the rest.
