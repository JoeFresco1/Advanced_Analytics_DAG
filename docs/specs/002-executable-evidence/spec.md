# Feature Specification: Execute and Inspect Evidence

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Proposal Slice A: run one fixed analysis, evaluate required checks, retain negative and failed outcomes, and open exact evidence in the viewer.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Inspect a measured result (Priority: P1)

An analyst opens a result from a fixed analysis and follows it to the actual
computation, data version, transformation, and validation evidence.

**Why this priority**: The first slice must prove that a user can inspect what
supports a reported value.

**Independent Test**: Run a fixed fixture analysis and open one displayed value;
follow each reference to a present, matching artifact.

**Acceptance Scenarios**:

1. **Given** a completed fixed analysis, **when** the user opens a result,
   **then** the data version, transformation, computation, check outcome, and
   scope of that result are visible.
2. **Given** an absent or altered result artifact, **when** a report is prepared,
   **then** the affected value is marked unsupported rather than displayed as
   a verified measurement.

### User Story 2 - See what a check actually found (Priority: P1)

A required check can run successfully and report that the analysis is invalid.
The user can inspect the observation, and supported presentation of the
dependent Claim is blocked.

**Why this priority**: Check execution and check outcome have different meanings.

**Independent Test**: Inject entity overlap into a split and inspect the check
record and the resulting claim state.

**Acceptance Scenarios**:

1. **Given** a check that computes twelve overlapping entities, **when** it
   finishes, **then** its process can complete while its analytical outcome is
   failed, and the twelve-entity observation remains visible.
2. **Given** a valid comparison with no improvement over baseline, **when** the
   result is recorded, **then** the negative finding remains a valid result and
   is not retried to seek a favorable value.
3. **Given** a required check that is missing, stale, or renamed, **when** a
   claim is assessed, **then** the policy coverage gap prevents supported use.

### User Story 3 - Recover an interrupted publication (Priority: P1)

After a crash or concurrent writer conflict, the user sees only committed
evidence and can resume without duplicate logical results.

**Why this priority**: Partial or mismatched evidence would make every later
claim trace unreliable.

**Independent Test**: Interrupt a fixture between artifact creation and
publication, then resume and inspect the manifest and report.

**Acceptance Scenarios**:

1. **Given** a crash before evidence publication, **when** the run resumes,
   **then** no partial success is shown and one logical result is committed.
2. **Given** a check for an older artifact, **when** a newer artifact is
   presented, **then** the older check does not qualify it.
3. **Given** evidence committed before acknowledgement is lost, **when** the
   run resumes, **then** the user sees exactly one committed result and the
   repeated publication does not create a second logical result.
4. **Given** two writers using the same expected manifest revision, **when**
   they race to publish different results, **then** one commit wins, the other
   receives a revision-conflict result, and neither artifact is overwritten.

### Edge Cases

- A task exits successfully without its declared result.
- A checker writes malformed output or exits unexpectedly.
- A validator reports “not applicable” without a policy-supported reason.
- Two computations try to publish to the same logical result at once.
- Byte serialization changes while the declared values remain equivalent.
- Evidence is deleted or redacted after publication.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The integrated fixed-analysis path MUST carry feature 009's
  exact plan, program, environment, input, and output bindings into the
  user-visible evidence record.
- **FR-002**: The integrated trace MUST display feature 010's exact check
  subject, validator, policy, observation, outcome, and evidence references.
- **FR-003**: The integrated path MUST preserve feature 010's check outcome
  separately from feature 009's process completion state.
- **FR-004**: Required-check coverage MUST be assessed against a fixed policy
  revision that a worker cannot silently weaken. Supported-presentation
  eligibility MUST follow feature 010 FR-007 for every dependent Claim.
- **FR-005**: Well-formed negative results and failed checks MUST remain in the
  evidence history even when they prevent claim qualification.
- **FR-006**: Reported numerical values MUST bind to actual result selectors;
  missing, stale, or altered evidence MUST prevent supported presentation.
- **FR-007**: The integrated path MUST use feature 009 FR-003/FR-004's atomic
  publication and recovery contract for both worker and viewer reads.
- **FR-008**: The integrated trace MUST show feature 009 FR-005's comparison
  rule and canonical reproduction state without inventing another state.
- **FR-009**: The user MUST be able to open a result trace and see missing or
  invalid links rather than a generic “verified” label.
- **FR-010**: Availability changes, including redaction, MUST be visible without
  erasing the historical record that evidence once existed.

### Key Entities

- **Computation**: One authoritative run on fixed inputs and program version.
- **Dataset and transformation versions**: The source and derivation of values.
- **Result**: Measured quantities, units, denominator, scope, and artifacts.
- **Check result**: A validator's observation and interpretation of fixed subjects.
- **Evidence manifest**: The committed references the user can inspect.
- **Claim**: An interpretation with a canonical evidence state and a separate
  supported-presentation eligibility assessment.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: All displayed values in the fixed fixture open a complete trace
  to their current result and data version; zero stale checks are accepted.
- **SC-002**: Every seeded missing-artifact, overlap, and omitted-check fault
  is surfaced and prevents unsupported promotion.
- **SC-003**: The null-improvement fixture produces one valid negative result
  with zero automatic attempts to obtain a positive result.
- **SC-004**: Pre-commit crash, post-commit acknowledgement loss, and a
  conflicting writer produce zero partial published results and zero duplicate
  logical results after recovery.
- **SC-005**: Users can identify the observed check value and its subject from
  the result trace without reading process logs.
- **SC-006**: The integrated trace shows each reproduction fixture's declared
  comparison rule and canonical state; byte-only serialization differences
  are distinguished from numerical mismatches in the user view.
- **SC-007**: The fixed-analysis trace exposes exact Plan, program digest,
  environment, input version, and output artifact IDs from feature 009;
  omitting one prevents a supported display.
- **SC-008**: A malformed or crashed checker becomes `error`, and a required
  `not_applicable` without a frozen-policy reason blocks the dependent Claim;
  neither is converted to pass by a zero task exit code.

## Assumptions

- This feature uses a fixed, known analysis. Agent-authored question framing
  and code generation arrive in feature 003.
- A source may be local for this fixture; source connector semantics arrive in
  feature 004.
- Passing a check proves only its declared condition, not general statistical
  correctness.

**Proposal trace**: Slice A; §§5, 9, 11, 15; T01–T04, T10, T13–T15, T22–T23, T52;
invariants I01–I04, I06, I08, I12, I14.
