# Feature Specification: Run Required Analytical Checks

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Execute mandatory validators on exact artifacts and separate their analytical outcomes from process success.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - See a failed check (Priority: P1)

An analyst sees that an overlap validator ran, observed twelve shared
entities, and rejected the condition without deleting the result or rerunning
for a more favorable split.

**Why this priority**: Check execution and check outcome have different meanings.

**Independent Test**: Inject entity overlap and inspect process, check, and
claim eligibility records.

**Acceptance Scenarios**:

1. **Given** twelve overlapping entities, **when** validation finishes,
   **then** its outcome is fail with the count even if the command completed.
2. **Given** that failure, **when** work continues, **then** the affected
   Claim's supported presentation is blocked and no automatic attempt seeks
   a passing result.

### User Story 2 - Enforce required policy (Priority: P1)

An analyst can see which checks were required, performed, inconclusive, or
not applicable under the fixed policy.

**Why this priority**: A worker cannot remove a failed check after the fact.

**Independent Test**: Omit, rename, or replace a required validator with a
permissive worker-authored one.

**Acceptance Scenarios**:

1. **Given** a missing or stale check, **when** a claim is assessed,
   **then** it cannot be presented as supported.
2. **Given** a justified not-applicable check, **when** assessed, **then**
   that status remains distinct from pass and missing.

### Edge Cases

- A checker crashes or writes malformed output.
- A check passes on an older artifact or policy version.
- A candidate fails to beat baseline while integrity checks pass.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Required checks MUST have executable validators, exact
  subjects, policy and validator versions, and declared outcomes.
- **FR-002**: Outcomes MUST distinguish pass, fail, inconclusive, not
  applicable, and error from command completion.
- **FR-003**: The controller MUST verify fixed-policy coverage; a worker
  MUST NOT omit or weaken a required validator.
- **FR-004**: A changed artifact or policy MUST make an old pass insufficient.
- **FR-005**: A valid negative analytical result MUST remain an observation,
  not a process failure or retry trigger.
- **FR-006**: Check summaries MUST state tested failure classes and limits.
- **FR-007**: For each dependent Claim, the controller MUST set
  `supported_presentation_eligible = false` when any required condition has
  outcome `fail`, `error`, or `inconclusive`, or coverage `missing` or `stale`,
  or `not_applicable` without a frozen-policy-supported reason. Only a current
  `pass` or policy-justified `not_applicable` satisfies that condition. The
  result and failed check MUST remain inspectable; risk acceptance MUST NOT
  convert the outcome to pass or promote the Claim as supported.
- **FR-008**: A configured project owner MUST register each versioned
  method-policy profile with scope, required validators, permitted
  `not_applicable` reasons, claim rule, and authority route before it is used.
  Registration MUST validate the schema and policy fixtures. A worker MAY
  propose a profile but MUST NOT publish or relax one.

### Key Entities

- **Check policy**: Required conditions for a method and claim.
- **Check result**: Subject, observation, expectation, outcome, and evidence.
- **Claim eligibility**: Whether required evidence permits support.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Seeded overlap, stale-subject, omitted-check, and malformed
  cases all receive their correct distinct outcomes.
- **SC-002**: The negative-baseline fixture causes zero attempts made solely
  to obtain a favorable result.
- **SC-003**: Every passing fixture Check Result declares its tested failure
  classes and limits; zero check summaries assert an untested condition.
- **SC-004**: Each seeded `fail`, `error`, `inconclusive`, `missing`, `stale`,
  and unjustified `not_applicable` required condition blocks supported
  presentation of its dependent Claim; a justified `not_applicable` remains
  visibly distinct from pass.
- **SC-005**: An unregistered method profile and a worker-relaxed required
  check produce zero supported Claims; an owner-registered version passes
  schema and policy fixtures before the first dependent execution.
- **SC-006**: Each fixture validator has an executable command or function,
  exact subject IDs, validator and policy versions, and structured observed
  outcome; changing only the subject digest makes its former pass stale.

## Assumptions

- A project owner supplies initial trusted policies and validators.
- A worker may propose extra checks but cannot replace required ones.

**Proposal trace**: §9; T02–T04, T10, T23; I03–I04, I06.
