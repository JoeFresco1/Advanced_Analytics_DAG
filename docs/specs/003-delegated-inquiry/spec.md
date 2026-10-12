# Feature Specification: Answer a Delegated Analytics Question

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Proposal Slice B: a worker turns a user question and data into a bounded exploratory-to-predictive analysis, checks it, and returns an evidence-linked answer.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Ask a predictive question (Priority: P1)

An analyst asks whether a decline in recent usage helps predict churn beyond
tenure and subscription plan. The worker determines a defensible population,
time horizon, baseline, comparison, and evaluation, then returns an answer with
effect size, uncertainty, and limits.

**Why this priority**: This is the first complete delegated question-to-answer
path and the worked example in the proposal.

**Independent Test**: Give the worker the question and a fixed synthetic
relational fixture without a prewritten analysis script; evaluate its answer
against a withheld reference and inspect its evidence trace.

**Acceptance Scenarios**:

1. **Given** the churn question and data fixture, **when** the worker runs,
   **then** it states the population, observation grain, prediction cutoff,
   churn horizon, metric, baseline, and comparison before presenting a claim.
2. **Given** the null fixture, **when** usage does not improve prediction,
   **then** the worker reports that result with uncertainty and does not keep
   searching for a favorable comparison.
3. **Given** an unresolvable material definition of “active customer,” **when**
   the worker reaches that choice, **then** it records the alternatives or asks
   for the specific missing decision instead of silently selecting one.

### User Story 2 - Preserve exploration and plan history (Priority: P1)

The analyst can see what the worker inspected, when the plan was fixed, and
whether a later method change was informed by observed outcomes.

**Why this priority**: The product must prevent exploration from being
presented as if the final hypothesis had been fixed in advance.

**Independent Test**: Expose one development result, change the method, and
inspect both plan versions and the claim label. Also run a declared resampling
step that should remain within the original plan.

**Acceptance Scenarios**:

1. **Given** an outcome-informed method change, **when** the worker proceeds,
   **then** a new plan and execution are linked to the earlier exposure.
2. **Given** a declared resampling or search candidate, **when** it runs,
   **then** it remains a planned execution rather than being mislabeled as a
   post-observation revision.
3. **Given** a local worker that could read the withheld partition, **when**
   evaluation is shown, **then** the answer states that access isolation was
   not enforced.

### User Story 3 - Finish within a declared allowance (Priority: P2)

The worker stops after producing a supported answer or a precise, useful
partial outcome, even when the data cannot support the requested conclusion.

**Why this priority**: Open-ended agent analysis otherwise risks repeated
work, high cost, and fabricated confidence.

**Independent Test**: Run a looping-worker fixture and a no-usage-telemetry
fixture under explicit time and invocation limits.

**Acceptance Scenarios**:

1. **Given** exhausted discretionary work, **when** the worker stops,
   **then** a readable partial answer identifies completed evidence, missing
   evidence, and the stop reason.
2. **Given** a valid but imprecise result, **when** the answer is returned for
   internal use, **then** it can complete as an uncertain answer without a
   mandatory human interruption.
3. **Given** missing cost telemetry, **when** usage is reported, **then** cost
   remains unavailable while enforceable time and invocation limits remain.
4. **Given** a worker is replaced after viewing evaluation metrics, **when**
   the inquiry resumes, **then** its context includes the earlier exposure,
   the plan keeps its post-observation label, and remaining allowance does not
   reset.

### Edge Cases

- A join multiplies customer observations or a filter silently shifts the denominator.
- A feature comes from after the prediction cutoff, a label is immature, or
  preprocessing uses evaluation data.
- Evaluation metrics are opened during exploration.
- The worker changes code or policy after a successful check.
- A narrative report swaps a metric value, denominator, or unit.
- Optional exploration consumes the work reserved for required checks.
- A restarted worker receives an incomplete summary of earlier exposure.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The worker MUST retain the user's original question and record
  its operational interpretation, intended use, assumptions, and unresolved
  definitions.
- **FR-002**: The worker MUST record the population, observation grain,
  target, time boundaries, denominator, baseline, comparison, evaluation
  design, stopping rule, and required checks for a directed predictive claim.
- **FR-003**: The system MUST distinguish bounded exploration from a locked
  directed plan, and MUST preserve each substantive plan revision and prior
  outcome exposure.
- **FR-004**: Authoritative computations MUST use fixed source versions and
  program versions; unrecorded calculations from an opaque worker session
  MUST NOT support a published value.
- **FR-005**: The worker MUST reconcile source-to-analysis transformations,
  including joins, exclusions, row and entity counts, and time cutoffs.
- **FR-006**: A claim that X adds predictive value MUST compare a model with X
  to a suitable baseline under comparable evaluation conditions.
- **FR-007**: The required checks MUST address split overlap, time-available
  features, target maturity, and preparation confined to training data; the
  system MUST state the limits of those checks.
- **FR-008**: The answer MUST distinguish predictive improvement from causal
  influence, and non-improvement from proof that X has no relationship to Y.
- **FR-009**: The worker MUST observe bounded time, invocation, and branch
  allowances and preserve capacity for mandatory checks and final reporting.
- **FR-010**: The integrated worker path MUST expose the canonical inquiry
  outcome selected under feature 012 FR-007 and must not infer it from task
  exit codes.
- **FR-011**: Every reported numerical assertion MUST bind to its result,
  denominator, unit, and scope; incorrect wording or values MUST be surfaced.
- **FR-012**: A local withheld evaluation MUST be labeled as a workflow
  convention when the worker's access is not technically isolated.
- **FR-013**: A restarted or replacement worker MUST inherit prior plans,
  exposure, results, and remaining budget.
- **FR-014**: The integrated inquiry MUST retain a valid null or negative
  analytical outcome and MUST NOT retry computation solely to obtain a
  favorable result; process failure recovery remains governed by feature
  009 and analytical check outcomes by feature 010.

### Key Entities

- **Question**: User wording, operational interpretation, scope, and intended use.
- **Plan revision**: Directed method, comparison, partition roles, and limits.
- **Exposure**: Known or self-reported access to outcomes or evaluation data.
- **Dataset and transformation versions**: Fixed source and measurement lineage.
- **Result and claim**: Computed comparison and its scoped interpretation.
- **Inquiry outcome**: Final answer or precise reason work stopped.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: On the fixed signal, null, and boundary predictive fixtures,
  every completed answer compares the declared baseline and candidate under
  feature 013's locked classification rule and
  reports the estimate, interval, `delta`, and scope; inability to estimate
  is labeled with a specific failed prerequisite.
- **SC-002**: Each seeded denominator, temporal leakage, premature-label,
  and report-value fault is detected by its named check, blocks supported
  presentation of the affected Claim, and remains visible in the answer trace.
- **SC-003**: Outcome-informed plan changes preserve the earlier exposure in
  every run; declared search steps create zero false post-observation labels.
- **SC-004**: Three recorded runs under the demo allowance terminate with a
  supported or explicitly partial outcome and never exceed its enforceable
  invocation/time limits.
- **SC-005**: An analyst can reach every displayed metric's result, plan,
  transformation, and data version through the answer view.
- **SC-006**: The integrated inquiry fixture preserves the original question,
  operational interpretation, intended use, assumptions, and unresolved
  definitions; its directed Plan lists every FR-002 field. The first-screen
  answer separates predictive improvement, causal influence, and a negative
  result without claiming that non-improvement proves no relationship.
- **SC-007**: Six integrated stopping fixtures display the six canonical
  inquiry outcomes selected by feature 012; changing a task exit code alone
  changes none of the displayed outcomes.
- **SC-008**: Every authoritative fixture value resolves to its fixed source
  version, program digest, environment, and execution; a value computed only
  in an opaque worker session appears zero times as a supported report value.
- **SC-009**: Editing code or the required policy after a successful check
  invalidates that check for the changed execution; a swapped report value,
  denominator, or unit is blocked before supported presentation.

## Assumptions

- The first complete delegated workflow is predictive analysis on tabular
  data; other method profiles are handled separately.
- The demonstration uses synthetic, fixed reference cases and records the
  worker version, allowances, and evaluator rules.
- Source-specific reusable skills arrive in feature 004; this feature may
  operate on a supplied fixed data version.
- The initial withheld partition is workflow-withheld unless a later access
  boundary is explicitly proven.

**Proposal trace**: Slice B; §§4, 6–8, 10, 15, 17; T04–T12, T21,
T23–T24, T26–T27, T61; invariants I01–I02, I04–I05, I07–I08,
I10, I13–I14.
