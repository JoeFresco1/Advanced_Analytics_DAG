# Feature Specification: Publish and Recover Exact Evidence

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Record fixed computations and publish complete evidence without stale bindings or partial success after a crash.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Open the computation (Priority: P1)

An analyst identifies the exact plan, program, environment, source version,
attempt, and output behind a measured result.

**Why this priority**: Later checks and claims need unambiguous subject identity.

**Independent Test**: Execute a fixed fixture, alter one output, and assess
whether the old result binding still qualifies it.

**Acceptance Scenarios**:

1. **Given** a valid computation, **when** its result opens, **then** every
   declared input and output is present with matching identity.
2. **Given** a missing or changed output, **when** assessed, **then** the
   previous evidence cannot support the altered result.

### User Story 2 - Recover disruption (Priority: P1)

After interruption, an analyst sees only complete committed records and can
resume without duplicate logical results.

**Why this priority**: Partial publication would mislead later users.

**Independent Test**: Stop publication after artifact creation and resume the
same logical request.

**Acceptance Scenarios**:

1. **Given** a crash before commit, **when** readers inspect the run,
   **then** no partial result appears as committed.
2. **Given** two writers racing on one revision, **when** both publish,
   **then** one revision wins and the other reconciles.
3. **Given** the manifest commit succeeded but its acknowledgement was lost,
   **when** the same logical request is replayed, **then** it resolves to the
   committed revision without publishing a duplicate result.

### Edge Cases

- A completed command writes no declared output.
- A worker reuses an earlier execution's path.
- A crash occurs after commit but before the worker receives confirmation.
- Serialization changes while values remain equal.
- Evidence is later redacted under retention rules.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Each authoritative computation MUST bind exact plan, program,
  environment, source versions, and attempt before its result is accepted.
- **FR-002**: Candidate outputs MUST be validated for identity, presence,
  and integrity before becoming committed evidence.
- **FR-003**: Publication MUST appear atomic to readers. Of two different
  commits targeting the same expected manifest revision, exactly one MUST
  commit and the other MUST receive a revision-conflict result without
  overwriting it. Replaying the same commit ID MUST return the existing
  committed revision without a second logical result.
- **FR-004**: Recovery MUST preserve failed attempts and prevent duplicate
  logical results on repeated publication, including replay after a commit
  whose acknowledgement was lost.
- **FR-005**: Reproduction MUST declare byte, canonical-value, or numerical
  tolerance comparison and record the canonical `not_attempted`,
  `reproduced`, `mismatched`, or `unavailable` state.
- **FR-006**: Redacted evidence MUST be shown as unavailable without erasing
  permissible history.

### Key Entities

- **Computation**: Exact invocation and input bindings.
- **Artifact**: Versioned output with integrity and availability.
- **Commit revision**: Reader-visible validated references.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Zero altered or missing fixture outputs qualify as supported.
- **SC-002**: Pre-commit crash, post-commit acknowledgement loss, and
  concurrent-writer fixtures publish zero partial and zero duplicate logical
  results after recovery.
- **SC-003**: A value-preserving serialization change is distinguished from
  a numerical mismatch under the declared comparison rule.
- **SC-004**: A committed fixture Result binds the exact Plan, program,
  environment, source versions, attempt, and output digests. Reproduction
  fixtures produce all four canonical states with their declared comparison
  rules; a redacted source remains historically linked but currently
  unavailable.
- **SC-005**: A worker reusing an earlier Execution path with a different
  attempt or output digest produces zero newly committed Results and a
  visible identity error; the earlier committed Result remains unchanged.

## Assumptions

- This feature includes a minimal user-readable evidence record. Features 010
  and 011 add policy checks and a complete answer-first trace.
- Append-only application history is not tamper-proof against a local
  privileged process.

**Proposal trace**: §§5, 9, 11; T01–T02, T13–T15, T22, T52; I01, I12.
