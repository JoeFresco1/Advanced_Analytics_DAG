# Quickstart: Evaluate the Analytics Product

This guide describes the implementation acceptance path. All inputs are
synthetic/local fixtures. The worker must run through the production analytics
path; expected answers and planted faults are available only to the independent
evaluator after the worker returns.

## Prerequisites

- Complete graph dependencies `002`, `003`, `004`, `005`, `006`, `020`, `022`
  and `18-bootstrap`.
- The bootstrap fixture registry and lock resolve without digest drift.
- The configured project-owner metric approval is present and precedes the
  batch start.
- The local worker harness can invoke model `gpt-6-luna` with `xhigh` reasoning.
- Synthetic source grants and inquiry allowances are available. The local
  shell profile is reported as workflow-withheld, not filesystem-isolated.

## Validate the evaluator

```powershell
pnpm typecheck
pnpm test
pnpm smoke:viewer
```

The acceptance suite first checks deterministic scorer and release-gate cases,
then exercises held-out synthetic questions through the real worker path. The
worker packet contains no reference answer, relevance label or planted-fault
description. `018.test.ts` verifies packet/reference separation, pre-batch
approval and immutable digests, exact numerators/denominators, false blocking,
comparison fairness, audit sample limits, missing cost, and fail-closed
release behavior.

## Inspect results

Inspect the structured evaluation and release assessment emitted by the
acceptance path. For every failed gate, follow the metric's fixture IDs and
committed inquiry/evidence references to its exact numerator, denominator,
minimum, threshold and source observation. Confirm the summary shows separate
fixture-profile and general-reliability eligibility. Confirm unknown monetary
cost is `unavailable` and the isolation label matches the harness guarantee.

The final `verification.md` records actual command outcomes, live model and
harness settings, batch and registry references, metric counts, failed gates,
evidence links, and any blocked or unavailable observations. A test run is not
called passing when a required fixture is skipped or the live worker path is
replaced by an expected-answer stub.
