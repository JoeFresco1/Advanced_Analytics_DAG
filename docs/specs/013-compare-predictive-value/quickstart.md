# Quickstart: Compare Predictive Value

This guide is for the implementation and acceptance phase. It uses only the
synthetic/local predictive fixtures registered and locked by 18-bootstrap.
It does not expose reference answers to the computation request.

## Prerequisites

- Features 001, 007, 008, 010 and 18-bootstrap provide the Python package,
  strict models, source/data lineage, locked Plan/exposure, required-check
  policy and fixture registry.
- Python and dependencies are installed from the existing uv lock.
- Node dependencies are installed from the repository lock.

## Focused validation

Run Python syntax validation from the repository root:

~~~powershell
uv run --locked --project python python -m compileall -q python/analytics_worker/predictive.py
~~~

Check the TypeScript facade:

~~~powershell
pnpm typecheck
~~~

Run the feature's synthetic producer/consumer acceptance file:

~~~powershell
pnpm exec tsx --test src/analytics/acceptance/013.test.ts
~~~

The acceptance file runs the real predictive calculator and facade. It
asserts positive, tightly below-margin and low-power null states for the
predeclared log-loss and ROC AUC fixtures; exact evidence binding; and failed
validity states for future features, immature labels, entity/time leakage,
global preparation, tuning or search changes, unsupported dependence, and
changed comparison scope. A locally readable withheld partition remains
labeled workflow-withheld.

## Final validation

~~~powershell
pnpm test
~~~

The repository test command builds the TypeScript project and executes its
compiled acceptance tests, including feature 013. Record actual command
results and per-requirement evidence in verification.md. This planning
artifact has not run product tests.

## Expected interpretation

On valid comparisons, the TypeScript facade applies the locked interval and
delta rule after current required-check eligibility. On an invalid
comparison, the Claim is unsupported and has no predictive classification.
A valid interval that spans delta is inconclusive. None of these analytical
outcomes triggers a favorable-result retry.
