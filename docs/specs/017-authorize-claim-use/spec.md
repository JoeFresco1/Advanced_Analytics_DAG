# Feature Specification: Review a Claim for Intended Use

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Evaluate exact current evidence when a claim is used for a consequential decision or external publication.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Promote an internal claim (Priority: P1)

A user takes a routine internal finding toward a consequential decision. The
product assesses current evidence and the configured use policy before
recording any authorization.

**Why this priority**: Stakes depend on how the claim will be consumed, not
only on its analytical mode.

**Independent Test**: Request consequential use of a fixed descriptive claim,
then change its supporting result and reassess.

**Acceptance Scenarios**:

1. **Given** a consequential use request, **when** evidence is assessed,
   **then** the current claim, result, check, and policy revisions are named.
2. **Given** changed material evidence, **when** the prior authorization
   opens, **then** its stale status is visible.

### User Story 2 - Record a real decision (Priority: P2)

A decision maker can accept a known uncertainty for a specified action without
the system claiming the finding became statistically certain.

**Why this priority**: Decisions and evidence are distinct organizational facts.

**Independent Test**: Authorize a documented risk acceptance and inspect the
claim state and decision record before and after.

**Acceptance Scenarios**:

1. **Given** a focused review, **when** the authority accepts risk, **then**
   the rationale, use, actor, and external action reference are recorded.
2. **Given** the same claim for routine internal use, **when** it is
   inconclusive, **then** it can still be returned honestly without a
   mandatory human interruption.
3. **Given** conflicting or negative evidence for a consequential use,
   **when** authorization is requested, **then** the gate shows that evidence
   and cannot issue ordinary `authorized`; any risk acceptance names the
   uncertainty, actor, rationale, and exact use.
4. **Given** a reviewer opens the trace but submits an empty rationale,
   **when** approval is attempted, **then** the assessment remains
   `pending_review` and no authorization is issued.

### Edge Cases

- A reviewer opens a trace but provides no substantive rationale.
- A claim was outcome-informed and later approved for exploratory use.
- An internal report is saved but never externally published.
- Policy changes after an authorization.
- The configured authority is revoked after an authorization.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Use assessment MUST bind exact claim, result, check, review,
  and policy revisions to the requested use.
- **FR-002**: Consequential uses MUST require the configured authority,
  while routine uncertain internal answers may complete honestly.
- **FR-003**: A review gate MUST present the relevant trace and record actor,
  rationale, unresolved issues, and outcome.
- **FR-004**: Risk acceptance MUST remain a use decision and MUST NOT change
  evidence or historical plan-exposure labels.
- **FR-005**: A changed bound revision or intended-use dimension under the
  canonical material-change rule MUST make dependent
  use assessments `stale` pending a new assessment; the old authorization
  remains inspectable as a historical decision.
- **FR-006**: A decision record MUST identify the selected action, deciding
  actor, exact claim revisions, time, and an external action reference.
- **FR-007**: Use assessments MUST use the canonical authorization states
  `not_assessed`, `pending_review`, `authorized`, `risk_accepted`, `denied`,
  `stale`, and `revoked`. A material evidence, scope, or policy change MUST
  yield `stale`; withdrawal of the configured authority MUST yield `revoked`;
  reassessment MUST create a new linked assessment rather than editing the
  historical one.
- **FR-008**: A review with no substantive rationale MUST NOT transition to
  `authorized` or `risk_accepted`. A conflicting or negative-evidence claim
  for consequential use MUST show those facts at the gate and requires an
  explicit, use-scoped `risk_accepted` assessment if policy permits proceeding.
- **FR-009**: Before use assessment, state MUST be `not_assessed`. A complete
  review with an explicit negative authority decision MUST become `denied`;
  a missing rationale or unresolved required decision MUST remain
  `pending_review`. When current, methodologically comparable Results support
  materially opposing answers to one scoped Claim, the worker or named
  reviewer MUST record a `conflicting` Claim reassessment with compared
  Result IDs and a reason; the controller verifies bindings, not semantics.

### Key Entities

- **Use assessment**: State and exact evidence evaluated for one requested
  use; this record carries staleness or revocation.
- **Review**: Actor, rationale, and unresolved issues.
- **Decision**: Chosen action and supporting claim revisions.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Zero fixture consequential-use requests proceed without their
  configured authorization.
- **SC-002**: Every material evidence change in the fixture marks affected
  prior authorizations stale.
- **SC-003**: Risk acceptance changes zero historical evidence states or
  post-observation labels.
- **SC-004**: Revoking an authority marks every affected current fixture use
  assessment `revoked`; a later new assessment keeps the old decision visible.
- **SC-005**: Empty-rationale approval attempts issue zero authorizations;
  conflicting and negative-evidence consequential fixtures never receive
  ordinary `authorized` status without their evidence being resolved.

- **SC-006**: Each approved decision fixture records the selected action,
  deciding actor, linked Claim versions, and external action pointer; a
  missing field prevents the Decision record from being finalized.
- **SC-007**: A use before request is `not_assessed`; explicit rejection is
  `denied`; an empty-rationale request remains `pending_review`. Two current
  comparable opposing Results yield a recorded `conflicting` reassessment,
  while differing populations yield separate scoped Claims.
- **SC-008**: The gated fixture displays the exact Claim, Result, Check,
  Review, and policy revisions and records reviewer, rationale, unresolved
  issues, requested use, and outcome; a missing required field prevents
  authorization.
- **SC-009**: A policy-permitted consequential use of a conflicting or
  negative-evidence Claim records `risk_accepted` with the named actor,
  uncertainty, and exact use; without that explicit assessment the use stays
  blocked and Claim evidence state is unchanged.
- **SC-010**: Merely saving an internal report records no external-publication
  authorization; an actual publication request creates a separately scoped
  assessment under the configured policy.

## Assumptions

- The organization configures who can authorize each use; the worker does
  not appoint itself.
- Off-platform copying is outside observable use events.

**Proposal trace**: §12; T16, T21, T23; I05, I09.
