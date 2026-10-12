# Feature Specification: Answer a Planned Inferential Question

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Proposal Slice E: extend the working question-to-answer product with one explicit inferential method profile and its failure cases.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Estimate a group difference (Priority: P1)

An analyst asks, “How different are renewal rates between these two customer
groups?” The worker defines groups, eligible population, time period, unit of
analysis, and the exact difference to estimate before inspecting the directed
result. It returns the estimate with an appropriate uncertainty interval and
limits of interpretation.

**Why this priority**: The product needs a concrete hypothesis-testing path
that does not collapse evidence into a significance flag.

**Independent Test**: Use a fixed reference fixture with known group rates and
repeat observations; inspect the plan, result, uncertainty method, and answer.

**Acceptance Scenarios**:

1. **Given** the renewal-rate question, **when** the worker proceeds,
   **then** it states the groups, eligible population, denominator, period,
   estimand, dependence assumptions, and planned comparison.
2. **Given** a valid estimate, **when** it answers, **then** it reports the
   estimated difference, interval, assumptions, and practical context rather
   than only a thresholded significance label.
3. **Given** an imprecise estimate, **when** it answers, **then** it does not
   claim equality or no meaningful difference without a suitable design.

### User Story 2 - Surface invalid inference (Priority: P1)

The analyst can see when repeated customers, changed group definitions,
post-observation subgroup selection, or multiple comparisons weaken the
conclusion.

**Why this priority**: An interval computed on the wrong analysis unit can
look precise while being misleading.

**Independent Test**: Use seeded clustered observations, missing group labels,
and a favorable subgroup selected after inspecting results.

**Acceptance Scenarios**:

1. **Given** repeated observations per customer, **when** an analysis assumes
   independent rows, **then** the dependence check identifies the mismatch
   and blocks supported presentation until a valid uncertainty method runs.
2. **Given** an outcome-informed subgroup change, **when** the worker reports
   it, **then** a new plan and exposure history are visible and the result is
   not presented as an untouched confirmatory test.
3. **Given** an unplanned family of comparisons, **when** the worker selects
   one, **then** selection and exposure are recorded, the selected comparison
   is labeled exploratory, and a pre-specified confirmatory label is blocked.
4. **Given** repeated unplanned inspection of a group difference, **when**
   the worker stops after a favorable look, **then** the exposure is recorded
   and the original fixed-look confirmatory claim is blocked. A separately
   seeded declared sequential plan follows its locked inspection rule.

### Edge Cases

- One group is too small for the proposed uncertainty procedure.
- Category membership is missing or changes across periods.
- The sample is not representative of the population named in the question.
- A worker infers gender or another sensitive group label from names.
- The interval method cannot handle the observed dependence structure.
- The worker uses an association to claim that changing group membership
  would cause renewal to change.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The inferential profile MUST define an estimand, population,
  observation unit, group definitions, period, planned comparison, and
  uncertainty interpretation before directed execution.
- **FR-002**: The worker MUST record exposure and distinguish planned
  comparisons from outcome-informed revisions.
- **FR-003**: The worker MUST assess whether sampling, repeated observations,
  and missingness support the selected uncertainty procedure.
- **FR-004**: The worker MUST identify the family of comparisons and disclose
  every unplanned selection. An unplanned selected comparison MUST remain
  exploratory and cannot use a pre-specified confirmatory label; a planned
  multiplicity procedure MUST be declared before the relevant outcome access.
- **FR-005**: The answer MUST report estimate magnitude, denominator, interval
  type, assumptions, and practical context together.
- **FR-006**: A non-significant or imprecise result MUST NOT be presented as
  evidence of equality or no meaningful effect without a design that
  establishes the relevant practical margin.
- **FR-007**: The worker MUST NOT infer sensitive group membership from names
  or silently collapse missing and distinct categories.
- **FR-008**: An observational group comparison MUST NOT be described as a
  causal intervention effect without a separate valid causal design.
- **FR-009**: The result and all method-specific checks MUST use the same
  inspectable evidence and use-assessment path as earlier features.
- **FR-010**: An unsupported inferential question MUST yield a scoped
  limitation rather than a generic test chosen merely because it can run.
- **FR-011**: The initial profile MUST estimate the difference in two
  predeclared renewal proportions using a declared customer-level observation
  unit. The plan MUST fix interval method, confidence level, dependence
  handling, and any sequential inspection rule before directed execution.
  Repeated customer observations MUST be reduced to a valid customer-level
  measure or analyzed with a justified cluster-aware interval.

### Key Entities

- **Inferential plan**: Question-specific estimand, sampling unit, procedure,
  comparison family, and uncertainty assumptions.
- **Group definition**: Membership rule, missingness, and period.
- **Estimate**: Difference with units, denominators, and uncertainty method.
- **Method check**: Evidence about assumptions, applicability, and selection.
- **Scoped claim**: The interpretation permitted by the design and evidence.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: The valid reference fixture produces an estimate and interval
  consistent with its declared comparison rule and reference evaluator.
- **SC-002**: Seeded repeated-unit and missing-group faults identify the
  affected unit or denominator and block supported presentation until
  corrected; post-observation subgroup selection is labeled exploratory,
  preserves exposure, and blocks a pre-specified confirmatory label.
- **SC-003**: The imprecise/null fixture produces zero claims that a lack of
  significance proves equality or no practical difference.
- **SC-004**: Every displayed estimate opens the same complete evidence trace
  used by the predictive workflow.
- **SC-005**: In three recorded runs of the valid and faulted fixtures, zero
  reports make an unsupported causal claim.
- **SC-006**: The valid two-group fixture matches the reference risk
  difference and declared 95% interval within predeclared numerical tolerance;
  the undeclared repeated-look fixture has zero confirmatory claims, while
  the planned sequential fixture records every look under its locked rule.
  Its Plan includes the estimand, population, customer unit, group
  definitions, period, comparison, and uncertainty interpretation.

- **SC-007**: A question outside the initial two-group rate-difference method
  produces a scoped unsupported-method result, with zero fabricated estimate
  or inferential claim.
- **SC-008**: Name-derived sensitive-group labels are rejected in the
  group-comparison fixture; missing membership remains a separate category
  or an explicit exclusion with its denominator shown.
- **SC-009**: A too-small group, nonrepresentative sample for the named
  population, and dependence structure unsupported by the selected interval
  method each produce a scoped limitation and zero unqualified inferential
  Claims; the failed assumption is named separately in each fixture.

## Assumptions

- This feature adds one two-group rate-difference profile. It does not claim
  support for all hypothesis tests, causal designs, survival analysis, or
  general statistical consulting.
- Group labels are supplied as data or owner definitions; the worker does not
  derive sensitive identity from personal names.
- Reference fixtures include a declared sampling structure and known fault
  variants so the evaluator can distinguish valid caveats from failures.

**Proposal trace**: Slice E; §§7–8, 12; T06–T07, T54–T55, T58;
invariants I02, I05, I07–I08, I14.
