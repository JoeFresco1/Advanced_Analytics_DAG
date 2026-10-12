# Feature Specification: Compare Predictive Value

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Answer whether a candidate feature adds prediction beyond an existing baseline using fair, time-valid evaluation.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Compare usage against a baseline (Priority: P1)

An analyst asks whether declining usage improves churn prediction beyond
tenure and plan. The worker returns the measured improvement or lack of one,
its uncertainty, and the limits of the comparison.

**Why this priority**: Merely including usage in a model does not show that
usage adds predictive value.

**Independent Test**: Run synthetic signal, tightly estimated below-margin,
and imprecise null variants against a withheld reference evaluator.

**Acceptance Scenarios**:

1. **Given** the signal fixture, **when** the comparison runs, **then** it
   evaluates a declared baseline and candidate under comparable conditions.
2. **Given** a tightly estimated below-margin variant, **when** the comparison
   finishes, **then** it reports `meaningful_improvement_excluded` without
   searching for a positive run.
3. **Given** an imprecise null variant, **when** the comparison finishes,
   **then** it reports `inconclusive` rather than proving no improvement.

### User Story 2 - Catch invalid evaluation (Priority: P1)

The analyst can see when a future feature, immature label, shared entity, or
globally fitted preparation invalidates the evaluation.

**Why this priority**: A numerically impressive metric can be unusable if it
depended on information unavailable at prediction time.

**Independent Test**: Inject each known leakage class into the fixture.

**Acceptance Scenarios**:

1. **Given** a post-cutoff feature, **when** evaluated, **then** the affected
   predictive Claim's supported presentation is blocked with the specific
   failed condition visible.
2. **Given** disjoint rows but repeated customers across partitions,
   **when** checked, **then** entity overlap is still detected.

### Edge Cases

- Label maturity differs across cohorts.
- A search candidate was declared before evaluation; another was added after.
- The holdout was workflow-withheld but locally readable.
- A better metric arises from a changed population or metric definition.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: A claim of added predictive value MUST compare a candidate to
  a declared baseline on comparable population, metric, and evaluation.
- **FR-002**: Prediction time, feature availability, target horizon, and
  label maturity MUST be explicit.
- **FR-003**: Evaluation MUST account for entity dependence, time ordering,
  preparation fit, tuning separation, and declared search scope.
- **FR-004**: Results MUST include the comparison magnitude, uncertainty,
  denominator, and applicable population. Before evaluation, the plan MUST
  fix the primary metric and favorable direction, minimum meaningful gain
  `delta`, interval level, uncertainty method, resampling unit, and any
  repeated-evaluation procedure. The comparison statistic MUST be transformed
  into a favorable-direction gain: candidate minus baseline for a
  higher-is-better metric, baseline minus candidate for a lower-is-better
  metric. `delta` MUST be nonnegative and use that gain's units.
- **FR-005**: A null or negative improvement MUST remain a valid outcome and
  MUST NOT be described as proof of no possible relationship.
- **FR-006**: The answer MUST avoid causal claims from predictive comparison.
- **FR-007**: A locally readable withheld partition MUST be labeled as
  workflow-withheld rather than independently isolated.
- **FR-008**: On a valid comparison, the controller MUST apply the locked
  decision rule to the favorable-direction gain interval: `improvement` when
  its lower bound exceeds `delta`, `meaningful_improvement_excluded` when its
  upper bound is at most `delta`, and `inconclusive` when the interval spans
  `delta`. The second label means the locked procedure rules out the
  predeclared meaningful gain at its declared interval level; it does not
  prove no relationship or no smaller gain. Failed required validity checks
  MUST produce an unsupported predictive Claim instead of any classification.

### Key Entities

- **Baseline and candidate**: The planned comparable procedures.
- **Evaluation partition**: Data role, time, entity scope, and access profile.
- **Predictive result**: Difference in declared metric with uncertainty.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: The predeclared signal fixture is classified `improvement` and
  the predeclared tightly estimated below-margin fixture is classified
  `meaningful_improvement_excluded` by the locked interval and `delta` rule. A
  low-power null fixture whose interval spans `delta` is `inconclusive`.
  The same three classifications hold for higher-is-better and
  lower-is-better metric fixtures after the declared sign transform.
- **SC-002**: Every seeded future-feature, immature-label, entity-overlap,
  and preparation-leakage case blocks an unsupported predictive claim.
- **SC-003**: Zero null-fixture runs retry solely to obtain improvement or
  claim that a non-improvement proves no relationship.

- **SC-004**: The predictive comparison fixture's answer contains zero
  causal assertions and identifies the predictive estimand and evaluation
  population.
- **SC-005**: The locked Plan names prediction time, feature availability,
  target horizon, label maturity, primary metric, direction, `delta`, interval
  method and level, resampling unit, search scope, and tuning separation.
  Seeded post-result search expansion or tuning on evaluation data prevents
  a planned supported comparison.
- **SC-006**: Baseline and candidate use the same predeclared population,
  metric, split, evaluation time, and uncertainty procedure; a changed
  denominator or metric prevents an added-predictive-value Claim even when
  the candidate's displayed score is higher.

## Assumptions

- The first workflow uses tabular data and one planned baseline comparison.
- Reference fixtures declare expected interval tolerance and classification
  before worker evaluation; they include both a tight below-margin case and
  an imprecise null case so uncertainty is not hidden by fixture sizing.
- A passed known leakage check cannot prove absence of every leakage class.

**Proposal trace**: §§7, 17; T04, T07–T09, T24; I04, I07, I13.
