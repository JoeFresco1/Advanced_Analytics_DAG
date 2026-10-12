# Implementation Plan: Compare Predictive Value

**Branch**: 013-compare-predictive-value | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from /specs/013-compare-predictive-value/spec.md

## Summary

Implement a real paired baseline/candidate prediction comparison in the
Python calculator already bootstrapped by feature 001, and a TypeScript
predictive facade that binds the computation to its locked Plan, data and
environment evidence. Python fits both procedures on identical declared
training partitions, fits preprocessing only inside those partitions, and
computes the locked favorable-direction gain interval. TypeScript validates
the echoed binding and current required-check coverage, then applies the
locked three-way decision rule to the numerical interval before assigning a
Claim-level predictive comparison classification. A failed required check
leaves the Claim unsupported and without that classification.

The production caller is the delegated-inquiry controller in feature 003
through the frozen **computeAnalytics(request, services)** facade. The
inspection path is the existing Claim/Result/check evidence trace owned by
011. Feature acceptance exercises the Python producer and TypeScript
consumer through synthetic/local fixtures and preserves an inspectable
failure for leakage and unsupported dependence. This feature consumes the
shared Pydantic v1 contracts, generated schemas, fixture registry and locked
dependencies; it adds no shared schema or package dependency.

## Technical Context

**Language/Version**: TypeScript on the repository's Node 20+ target; Python
version selected by feature 001's uv-locked package.

**Primary Dependencies**: Feature 001's strict Pydantic contracts,
generated JSON schemas, Python dispatcher and locked predictive dependencies;
the neutral-execution package and shared result-binding adapter. No dependency
manifest or lock changes in this feature.

**Storage**: Exact request, execution, Result, output and Check Result
references use the established analytics sidecar and EvidenceRef contracts.
No analytics data is stored in or added to dag.run.json.

**Testing**: Feature-owned TypeScript acceptance file
src/analytics/acceptance/013.test.ts invokes the real Python mode through
the facade. Use uv run --locked for Python package checks, pnpm typecheck
for the TypeScript facade, and pnpm test for final acceptance.

**Target Platform**: Local Node and uv execution through the existing
neutral runner with argument-vector invocation and its declared command
timeout.

**Project Type**: Analytics product with a Python tabular calculator and
TypeScript admission, evidence and Claim-classification facade.

**Performance Goals**: Complete the frozen synthetic fixtures within the
existing admitted-work and per-command limits. This feature adds no separate
latency or throughput promise.

**Constraints**:

- Pydantic models and generated schemas from 001 remain the sole normative
  wire contract; do not create a second common or predictive wire model.
- Consume the existing Python lock and dependencies. Do not edit
  python/pyproject.toml, python/uv.lock, generated schemas, shared
  dispatch, shared checks, shared architecture, or another feature.
- Python calculates predictive values and intervals. TypeScript must wait
  for exact binding and current required-check eligibility before applying
  the locked Claim classification rule.
- Both procedures must use the same locked evaluation population, metric,
  split, time, transformation and uncertainty procedure. A changed denominator
  or metric cannot support added predictive value.
- Use only synthetic/local fixtures. A locally readable withheld partition
  remains labeled workflow-withheld; it is not described as independently
  isolated.
- Preserve negative, null, failed and incomplete evidence. No analytical
  outcome initiates retries seeking a favorable metric.

**Scale/Scope**: One tabular baseline/candidate comparison under one
registered predictive Plan revision and its finite predeclared search. The
first implementation supports only split, preprocessing, interval and
resampling combinations admitted by the frozen request and current check
policy; unsupported combinations fail closed.

**Predeclared fixture metrics**: The motivating synthetic churn comparison
uses binary log loss (lower is better) for the baseline-versus-usage
comparison. ROC AUC (higher is better) is a separate directional-control
fixture. Signal, tight below-margin and low-power null cases are declared for
both metric directions before evaluation; each fixture locks its own metric,
delta, interval level, method, resampling unit and tolerance. These choices
belong to acceptance Plans only. Runtime computation uses the exact metric
and parameters in its registered Plan and does not insert a default.

## Constitution Check

| Principle or constraint | Plan response | Gate |
| --- | --- | --- |
| I. Honest scoped answer | Return the measured gain, interval, denominator, evaluation population, limits and predictive estimand. A low-power interval remains inconclusive. | Pass |
| II. Execution differs from evidence | Bind exact request, Result, code, environment, data and check subjects. Keep a negative result; do not retry for a favorable metric. | Pass |
| III. Preserve plan exposure | Consume the locked Plan revision, declared search and exposure history. Post-result changes remain outcome-informed and cannot regain confirmatory status. | Pass |
| IV. Scoped project memory | Reuse EvidenceRef and source/data lineage; do not add memory, skill or answer-card stores. | Pass |
| V. Accurate bounded guarantees | Use the admitted neutral execution limits and label local workflow withholding accurately. | Pass |
| VI. End-to-end behavior | Connect Python producer, TypeScript facade, downstream 003 caller, existing 011 trace, and seeded failures through the feature acceptance file. | Pass |
| Product boundary and schema ownership | Keep statistical computation in Python, policy/binding/classification in TypeScript, and Pydantic as the only wire-schema authority. | Pass |

No constitution exceptions or complexity justifications are required.

## Design

### Plan and calculation boundary

Feature 008 owns Plan registration, lock state, exposure and
plan_interpretation. This feature reads its exact predictive registration
and does not reimplement registration. The request must carry, through the
existing mode model, the baseline and candidate procedures, feature
availability, prediction time, target horizon and cohort-specific maturity,
population/evaluation scope, temporal or group split, tuning separation,
finite locked search set, primary metric and units, favorable direction,
nonnegative delta, interval method/level, resampling unit/scheme, seed rule
and repeated-evaluation procedure.

The Python calculator uses only those locked definitions. It constructs
comparable baseline and candidate pipelines over the same eligible
observations. Each preprocessing transform is fit on its training partition
or training fold and then applied to its held-out partition. Search and
tuning use only the declared training-side separation; evaluation outcomes
are not used to select features, parameters, thresholds or models. It
calculates the metric for both procedures and the paired difference on the
same population.

For the synthetic churn example, the baseline is the predeclared tenure and
subscription-plan procedure and the candidate adds the predeclared recent
usage trend. The primary comparison uses binary log loss; the independent
ROC AUC fixture proves the higher-is-better transform. The signal,
tight-margin and low-power fixture variants are chosen and locked before
their execution, not selected after observing scores.

Map the raw difference into favorable units before calculating or applying
the practical margin:

| Locked direction | Favorable gain |
| --- | --- |
| Higher is better | candidate metric minus baseline metric |
| Lower is better | baseline metric minus candidate metric |

Calculate the interval using the locked method, level, resampling unit,
scheme and seed rule. Resample paired predictions/differences at the declared
independent unit. Do not silently downgrade an entity or time dependency to
row-wise resampling. A method/split/resampling combination that cannot
handle the observed dependence produces a failed required condition and an
unsupported Claim.

Python returns the measured values and uncertainty through the existing
strict PredictiveResultV1. It does not establish supported Claim state.
TypeScript uses the returned gain bounds and locked delta only after the
exact binding and all required checks are current and eligible. Its
Claim-level classification is:

| Predicate on favorable gain interval | Predictive comparison |
| --- | --- |
| Lower bound is strictly greater than delta | improvement |
| Upper bound is at most delta | meaningful_improvement_excluded |
| Interval spans delta | inconclusive |

The Claim label is distinct from Claim evidence state, Check outcome and
inquiry outcome. The TypeScript facade derives the authoritative label after
check eligibility. If the frozen Pydantic result includes a computed
classification field, TypeScript verifies it against the interval rule; that
field alone never authorizes a Claim label. A required check that fails,
errors, is inconclusive, missing, stale, or is not policy-permitted as
not-applicable results in an unsupported Claim and no Claim classification.
An ordinary low-power interval that spans delta with checks passing is a
valid inconclusive predictive comparison, not an unsupported Claim.

### Validity and evidence controls

The Python path and the TypeScript acceptance/facade enforce or consume these
method-specific conditions:

- Every feature is available by its observation's prediction time. A
  post-cutoff feature fails the temporal validity condition.
- Target labels have completed the declared horizon and are available by
  the evaluation cutoff. Check maturity by cohort, so an immature cohort is
  not made eligible by mature labels in other cohorts.
- Temporal partitions train only on earlier prediction times than the
  evaluation partition. Group partitions keep entity IDs disjoint. A split
  can satisfy both dimensions only when the locked design and checks verify
  both.
- Transformations fit on training data only, including within cross-validation
  and tuning folds.
- Search candidates and repeated looks match the locked Plan and exposure
  record. A post-result candidate, metric, population, split, or stopping
  change is outcome-informed and prevents a planned supported comparison.
- Baseline and candidate comparison uses the same eligible population,
  denominator, metric, split, evaluation time and interval procedure.
- The request/result echo run, inquiry, attempt, execution, Plan revision,
  exact sources and transformations, code digest, Python version, dependency
  lock digest, environment digest and seed. Declared output digests are bound
  to the Result evidence.
- The viewer/answer trace retains the local profile as
  workflow-withheld; access isolation not enforced where applicable.

Check results remain attributed to the required validator/policy and exact
subject references owned by 010. This feature consumes their eligibility
decision; it does not redefine check vocabularies or globally implement
check-policy evaluation.

### User paths and failure fixtures

The typed facade is the production mode entrypoint. Feature 003 is its
delegated-inquiry caller; the existing 011 viewer follows the committed
Claim → Result → check → execution → Plan/data/program/environment evidence
chain. The feature acceptance file runs the calculator through this path
using only the feature-scoped synthetic fixtures registered and locked by
18-bootstrap. It checks the valid positive, tight below-margin, low-power
null and lower-is-better cases, plus future-feature, label-maturity,
entity/temporal split, train-only preparation, evaluation tuning, unsupported
dependence, search-exposure, comparability and workflow-withholding cases.
Reference classifications/tolerances come from the frozen fixture evaluator;
the calculation code contains no fixture-specific output values.

## Project Structure

### Documentation (this feature)

~~~text
specs/013-compare-predictive-value/
├── plan.md
├── research.md
├── data-model.md
├── contracts/
│   └── predictive-comparison.md
├── quickstart.md
├── tasks.md
├── execution.json
└── verification.md
~~~

### Source Code (repository root)

~~~text
python/analytics_worker/predictive.py
src/analytics/predictive.ts
src/analytics/acceptance/013.test.ts
~~~

**Structure Decision**: Keep the Python calculator in the feature-owned
analytics worker module and the TypeScript evidence/Claim facade under
src/analytics/. All deterministic predictive fixtures and end-to-end
assertions stay in the graph-owned feature acceptance file; do not create
python/tests/test_predictive.py because node 013 does not own that path.

## Complexity Tracking

No additional projects, schema layer, scheduler, or shared abstraction is
introduced.

## Post-Design Constitution Check

The design retains all original gates: exact data and environment bindings;
training-only fitting and comparable denominators; separate execution,
check, Claim and inquiry states; immutable exposure; accurate local
withholding; and an inspectable producer/consumer/failure path. No accepted
requirement, checklist, policy threshold or reference answer is modified.
