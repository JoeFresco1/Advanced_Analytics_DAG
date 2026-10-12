# Feature Specification: Reassess Recurring Answers and Intended Use

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Proposal Slice D: rerun an analysis on a changed window, preserve earlier answers, reassess affected claims, and review claims for a consequential use.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Compare a new data window (Priority: P1)

An analyst reruns a known analysis on a newer window and can tell whether a
changed answer reflects new data, a changed metric definition, different
coverage, or a provisional stream window.

**Why this priority**: Repeated data sources are useful only if old and new
results can be compared honestly.

**Independent Test**: Run the same planned analysis on two fixed windows,
including a variant with a changed definition, then inspect the comparison.

**Acceptance Scenarios**:

1. **Given** a finalized new window with compatible definitions, **when** the
   analysis recurs, **then** a new result is linked to the prior result and
   the earlier answer remains inspectable.
2. **Given** a definition or population change, **when** the analyst compares
   periods, **then** the product identifies the difference and does not
   present an unqualified trend.
3. **Given** a provisional window, **when** the report is shown, **then** its
   completeness and lateness limits are visible.

### User Story 2 - Handle a correction (Priority: P1)

A late event or corrected extract changes a source version. The analyst can
find every dependent answer that may need reassessment.

**Why this priority**: Silent overwrites make historical decisions impossible
to audit.

**Independent Test**: Correct one earlier window and inspect the dependency
trail through results, claims, reports, source guidance, and use assessments.

**Acceptance Scenarios**:

1. **Given** a late correction, **when** it is ingested, **then** a new source
   version records the correction and affected claims are flagged.
2. **Given** evidence later deleted or redacted, **when** a historic claim is
   opened, **then** its current reproducibility is labeled unavailable while
   permissible historical metadata remains.

### User Story 3 - Evaluate a claim for a new use (Priority: P2)

A user wants to apply an existing descriptive or predictive claim to a
consequential decision or external publication. The product reassesses its
current evidence and records who authorized that use.

**Why this priority**: Claim type alone does not determine the harm of a
wrong answer. Authorization depends on what someone will do with it.

**Independent Test**: Promote a routine internal claim to a declared
consequential use, then change supporting evidence and test whether the
authorization becomes stale.

**Acceptance Scenarios**:

1. **Given** an internal claim requested for a consequential use, **when**
   use assessment runs, **then** it evaluates exact current evidence and
   requires the configured authority before that use proceeds.
2. **Given** human acceptance of uncertainty, **when** the use is authorized,
   **then** the exception and rationale are recorded without changing the
   evidence state to “supported.”
3. **Given** new material evidence, **when** a previous use assessment is
   inspected, **then** its stale status is visible while the historical
   decision remains auditable.

### Edge Cases

- A new window uses the same schema but changes business meaning.
- A late event arrives after a report has already been consumed.
- An internal answer is exported but not actually sent for publication.
- A reviewer opens the trace but provides no substantive rationale.
- A claim is inconclusive yet is useful as an internal answer.
- A policy changes after an authorization was granted.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The integrated recurring inquiry MUST expose feature 016
  FR-001's distinct source/Execution versions and preserved Results and
  Claims in the answer view.
- **FR-002**: The integrated answer MUST display the window identity and
  completeness recorded under feature 016 FR-002/FR-007.
- **FR-003**: The integrated trend view MUST consume feature 016 FR-003's
  comparison state; it MUST NOT restate an `unassessed` or `incomparable`
  pair as an unqualified change.
- **FR-004**: A source correction MUST use feature 016 FR-004's dependency
  traversal to identify affected records for re-evaluation; this milestone
  MUST expose the resulting impact in the answer and use view.
- **FR-005**: A requested use MUST be evaluated against exact claim, result,
  check, review, and policy revisions.
- **FR-006**: The product MUST distinguish routine internal answers from
  consequential decisions and external publication according to configured
  use policy.
- **FR-007**: The product MUST allow an inconclusive or conflicting result to
  complete a routine internal answer with an honest limitation; it MUST NOT
  silently recast that evidence as supported.
- **FR-008**: A focused human gate MUST present the relevant trace and record
  actor, rationale, unresolved issues, and authorized use when required.
- **FR-009**: Human risk acceptance MUST NOT change the historical evidence or
  post-observation labels.
- **FR-010**: Material evidence or policy changes MUST expose the `stale`
  use-assessment transition owned by feature 017 FR-005/FR-007 before a
  previous authorization is used again.
- **FR-011**: The integrated answer and use view MUST display feature 009
  FR-005's current reproduction assessment without deriving a new state
  from execution success or historic availability.

### Key Entities

- **Source window/version**: A bounded, completeness-labeled acquisition.
- **Correction relationship**: What changed in an earlier source version.
- **Claim revision**: A scoped interpretation linked to current and prior evidence.
- **Use assessment**: Evidence and policy evaluated for one requested use.
- **Review record**: The actor, rationale, unresolved issues, and outcome.
- **Decision**: A lightweight record of a chosen action and the claim revisions
  relied upon, with a pointer to the downstream action.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Across the recurring fixture, zero prior result or claim versions
  are overwritten; all new versions remain traceable to their source window.
- **SC-002**: Every seeded definition, coverage, and late-data change is
  surfaced before an unqualified trend or reuse decision. The integrated
  answer displays window boundaries, completeness, source position, lateness,
  and applicable label maturity from the focused window record.
- **SC-003**: A correction identifies all fixture claims and use assessments
  that depend on the corrected source version, and the answer/use view links
  the other dependency types named by feature 016 FR-004.
- **SC-004**: A consequential-use request cannot proceed without its required
  authorization; the same inconclusive claim can still be returned honestly
  for routine internal use.
- **SC-005**: On changed material evidence, every affected authorization is
  marked stale and the earlier decision remains inspectable.
- **SC-006**: The integrated use view shows the exact Claim, Result, Check,
  Review, and policy revisions; a gated review records actor, substantive
  rationale, unresolved issues, and authorized use. The same view displays
  `not_attempted`, `reproduced`, `mismatched`, and `unavailable` reproduction
  fixtures without deriving status from command exit or old availability.
- **SC-007**: Accepting a scoped risk for one use changes zero historical
  Claim evidence states or plan-exposure labels; the use view records the
  actor and accepted uncertainty separately.
- **SC-008**: Saving an internal report without an external-publication
  request causes zero publication authorizations. Opening a gated trace with
  no substantive rationale issues zero authorizations, while a later actual
  publication request follows its configured use policy.

## Assumptions

- The initial recurrence uses bounded extracts or finalized windows, not
  unbounded continuous processing.
- The authority for consequential use is configured by the organization;
  the worker does not appoint itself to that role.
- Off-platform copying cannot be detected, so use checks apply to uses
  requested or recorded in the product.
- Formal audit sampling can follow once routine review volume makes its
  disagreement rate meaningful.

**Proposal trace**: Slice D; §§12, 14–15; T16, T20, T22; invariants I05,
I08–I09.
