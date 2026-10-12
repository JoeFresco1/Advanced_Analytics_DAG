# Quickstart: Plan and exposure acceptance

This is the implementation-stage validation recipe. It uses only synthetic or local fixtures registered by 18-bootstrap. It does not require a live user source, change a fixture reference answer, or claim access isolation for a shell-readable partition.

## Prerequisites

- Feature 001 generated and checked in the four `PlanRegistrationV1` schemas and shared conformance vectors.
- Feature 009 provides committed run records, `recoverEvidence`, publication, and `executeStageV1`.
- Feature 18-bootstrap provides the locked synthetic inquiry, development/evaluation partitions, and planted outcome/restart cases.
- Dependencies installed in the repository checkout with the supported Node version.

## Run the feature path

From the repository root:

```powershell
pnpm typecheck
pnpm test
```

The acceptance test calls the production 008 facade with synthetic/local fixtures, registers an exploratory plan, locks a directed profile, and runs declared search/resampling candidates through 009's real `executeStageV1`. It injects an undeclared evaluation-metric look, an outcome-informed method revision, a code-only correction, a source skill containing an earlier outcome, review approval, and a committed-history rehydration request. Failed and conflicting exposure commits must return no release permit and must not invoke `executeStageV1`. The test validates the rehydration projection but does not simulate delivery to a worker; feature 003 owns that production controller assertion.

## Expected observations

- All four supported registration profiles validate against generated schemas; unsupported or mismatched profiles, missing/extra fields, invalid refs, and non-finite values fail closed.
- The directed plan contains population, target, comparison, partition design, search space, stopping rule, and required checks before its stage is admitted.
- A declared candidate and resample remain within the locked planned family after another result is observed.
- A relevant metric delivery has an actor, material, source/partition, time, basis, and host guarantee committed before the worker context is released.
- A population, method, split, search-space, or stopping-rule change creates a linked plan and keeps earlier exposure. A code-only repair has a new Execution/code digest and no false Plan rewrite.
- Rehydration from controller-supplied committed references retains every prior label and returns the exposure timeline and remaining-budget binding. A missing/unknown boundary stays `exposure_unknown`; 003 verifies that this result reaches a replacement worker.
- Human approval can authorize exploratory use but cannot restore pre-specification. A workflow-withheld local partition is not represented as enforced isolation.

The focused feature run validates the production 008 facade and real 009 stage path. When 003 is complete, its live-controller acceptance must compose the exported request/result hooks, replay T06, T07, T09, and T61 through the production CLI/controller, and inspect the resulting Plan/exposure timeline, including replacement-worker context delivery.
