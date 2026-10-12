# Node 99 implementation verification quickstart

This planning artifact defines the scenario that T004–T006 will exercise. It
is not the user-delivered `docs/QUICKSTART.md` and does not claim these product
commands or screens have run. The delivered quickstart must replace planned
steps with the exact observed arguments, outputs, IDs, routes, and limits.

## Prerequisites

- Graph prerequisites 018, 005, 006, 020, and 022 are merged in the isolated
  integration checkout.
- The configured local worker can run `gpt-6-luna` with `xhigh`; record its
  actual harness and version. If unavailable, report the live journey as
  blocked/unavailable rather than replacing it with a constant answer.
- The project, principal, grants, and source are synthetic/local fixtures
  admitted by the feature owners. Do not use real or confidential source data.
- Evaluator references remain outside worker context. Local shell access is
  labeled workflow-withheld if it can bypass product surfaces.

## User journey

From the repository root, substitute an admitted project path, question, and
synthetic source fixture for the placeholders. The final user guide records
the actual values and returned IDs.

```powershell
dag analytics ask --project <path> --question <text> --source <fixture> --model gpt-6-luna --reasoning xhigh
dag analytics answer show --inquiry <id> --open
dag analytics trace --claim <id>
dag analytics answers search --query <text> --period <range>
dag analytics ledger show --inquiry <id>
dag analytics source discover --source <fixture>
dag analytics ask --project <path> --question <second-question> --source <fixture> --model gpt-6-luna --reasoning xhigh
```

Verify that the first answer shows its scope, uncertainty, limitations, current
validity, and evidence links. Open its real answer route and the claim trace;
record actual IDs and the observed screen. Search must show accessible result
scope or an explicit `no_match`. The ledger must preserve null, negative,
failed, stopped, and incomplete attempts where they were produced.

The second inquiry must demonstrate automatic relevant answer recall and the
exact compatible source-skill version supplied to the worker. Its trace must
show that the referenced recipe was actually executed. Do not infer use from
the skill being available in the project.

Use the accepted feature-owned synthetic failure fixture to produce a failed
required check, then inspect its answer/trace and ledger through the same CLI
and browser surfaces. Do not add a made-up failure flag or plant a fault
explanation in worker context. If no documented fixture can produce that
journey through the frozen interface, record the capability as missing.

## Release checks to record

Run each from the repository root and capture actual exit/output:

```powershell
pnpm build
pnpm test
uv run --project python pytest
python scripts/audit_spec_coverage.py
pnpm smoke:viewer
pnpm exec tsx --test src/analytics/acceptance/99.test.ts
```

Run feature 018's complete evaluator through its implemented API after the
worker response; record the actual invocation, batch/run ID, threshold refs,
counts, denominators, measured metrics, cost as observed or unavailable, and
any failed gate. The structural spec audit is not a substitute for feature or
browser acceptance.

## Screen and evidence record

The final user quickstart includes only screens opened during T004–T006. Use
the real answer and evidence-inspector route emitted by the implementation;
do not guess route strings. Link a captured browser artifact when one exists.
If only an ephemeral local route was observed, state that retention limit and
record the route plus inquiry/claim/artifact IDs. Report local access and
holdout guarantees exactly as enforced by the selected harness.

No product command, live model call, evaluator run, or browser session was
performed while producing these task-plan artifacts.
