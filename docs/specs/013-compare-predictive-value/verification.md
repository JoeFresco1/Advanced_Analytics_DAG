# Verification: Compare Predictive Value

**Status**: Planning evidence map only. Product implementation and acceptance
commands have not been run in this task-plan stage.

The final feature-verification task must replace this status with actual
command results, fixture outcomes, exact binding evidence, observed limits
and the completed FR/SC/edge mapping. A planned case is not a passing result.

## Planned commands

| Check | Command | Expected evidence |
| --- | --- | --- |
| Python module syntax | uv run --locked --project python python -m compileall -q python/analytics_worker/predictive.py | The feature calculator compiles in the dependency-owned Python environment. |
| TypeScript facade | pnpm typecheck | Shared mode schema/facade types compile without a duplicate wire contract. |
| Feature scenarios | pnpm exec tsx --test src/analytics/acceptance/013.test.ts | Real Python and TypeScript paths satisfy valid and invalid synthetic fixture states. |
| Full acceptance | pnpm test | Project build and compiled acceptance suite pass, including feature 013. |

## Requirement evidence to record

| Requirement | Planned evidence |
| --- | --- |
| FR-001 | Same population, denominator, split, metric, time and uncertainty procedure for baseline/candidate; changed scope blocks the Claim. |
| FR-002 | Prediction time, availability, target horizon and cohort-specific maturity are bound and checked. |
| FR-003 | Temporal/group split, train-only preparation, declared search/tuning separation and resampling dependence are checked. |
| FR-004 | Computed metric, favorable gain, interval, denominator and population; locked direction, delta, interval method/level and resampling unit. |
| FR-005 | Tight negative and valid imprecise null remain distinct; null is not retried and does not claim no relationship. |
| FR-006 | Structured claim/report path states a predictive estimand and no causal conclusion. |
| FR-007 | Locally readable holdout is labeled workflow-withheld; access isolation is not claimed. |
| FR-008 | TypeScript applies the exact three-way interval/delta rule only after exact binding and required-check eligibility. |
| SC-001 | Positive, tight below-margin and low-power null classifications for the predeclared lower-is-better log-loss and higher-is-better ROC AUC fixtures. |
| SC-002 | Future feature, immature label, entity overlap and preprocessing leakage each block supported presentation. |
| SC-003 | Valid null fixture executes once and records no favorable-result retry. |
| SC-004 | The typed predictive result identifies its predictive target and evaluation population and makes no causal assertion; the 003 answer path consumes this structured result without promoting it to a causal conclusion. |
| SC-005 | Locked required parameters and search; post-result search/tuning cannot remain a planned supported comparison. |
| SC-006 | Baseline and candidate use matching population, metric, split, time and uncertainty protocol. |

## Edge cases to record

| Case | Required observed evidence |
| --- | --- |
| EC-001: Cohort label maturity differs | Immature cohort fails eligibility with the cohort/cutoff condition visible. |
| EC-002: Predeclared and post-result candidate search | Locked candidate is allowed; expansion after exposure is outcome-informed and cannot support the planned Claim. |
| EC-003: Workflow-withheld but locally readable holdout | Access label states workflow-withheld; access isolation not enforced. |
| EC-004: Changed population or metric | Comparability check blocks added-predictive-value support despite any higher displayed score. |

## Interpretation limits

Passing fixtures establish only the tested partition, feature availability,
maturity, preprocessing, search, dependence and interval properties. They do
not certify absence of every leakage class or prove that predictive
association is causal.
