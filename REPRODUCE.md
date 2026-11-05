# Reproduce the controlled end-to-end study



**Offline reproduction of the supplied evidence needs no AWS or model calls:**

```bash
"$ALL_ENV/bin/python" evals/article_e2e_20260921/analyze.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/verify.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/report.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/evidence_index.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/write_findings.py
```

The live commands below spend model tokens and create synthetic shoppers. Use a
fresh copy of the repository and first move its supplied `data` directory to
`data-original`, create an empty `data`, and copy `data-original/prompts.json` into
it. Do not overwrite the supplied evidence. New live runs require fresh review of
outcomes and narrative examples; published prose describes this frozen execution.

The Python environment needs httpx, websockets, openai and matplotlib. Application
dependencies come from the unchanged image digests; exact deployed versions and
source hashes are in `data/deployed_snapshot.json`. Request JSON records effective
model settings; absent fields use provider defaults. No model credential is copied
out of Kubernetes.

```bash
"$ALL_ENV/bin/python" evals/article_e2e_20260921/test_meter.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/run_batch.py --plan-only
"$ALL_ENV/bin/python" evals/article_e2e_20260921/provision.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/run_batch.py --pilot --pilot-round 1
"$ALL_ENV/bin/python" evals/article_e2e_20260921/run_batch.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/sdk_response_loss.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/export_final.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/collect.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/analyze.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/verify.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/report.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/evidence_index.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/write_findings.py
"$ALL_ENV/bin/python" evals/article_e2e_20260921/provision.py --cleanup
"$ALL_ENV/bin/python" evals/task_cost_study/package.py
```

The runner resumes completed folders. To make new measurements, copy this study
directory to a new checkout and archive its `data` directory before the commands;
copy the original `data/prompts.json` into the new empty `data` directory to reuse
the measured prompt versions (otherwise provision snapshots current live prompts).
keep the original evidence immutable. Do not run two copies simultaneously against
the same study deployments. The resource label/queue names are intentionally fixed
and cleanup selects only these isolated resources. Synthetic customer registrations
and test traces remain in the backend as evidence.

For offline reproduction of numbers and charts, run only `analyze.py`, `verify.py`
and `report.py` against the supplied evidence. No AWS or model calls are involved.

The first pilot had a worker-transition accounting gap and two local driver failures
caused by a module filename collision. They remain in pilot rows, separate from the
fixed main schedule. A fresh run need not reproduce these setup defects.

Controlled fault experiments intercept exactly one logical task call before Azure:
one injected 429 followed by a genuine SDK retry, or three injected 503 responses
exhausting `max_retries=2`. These rejected attempts incur no provider model charge
because they never reach Azure. They do not establish Azure's billing behavior on
real server failures or client timeouts.
