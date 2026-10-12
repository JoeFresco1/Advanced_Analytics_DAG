# Feature Specification: Activate and Reuse Learned Skills

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Validate skill candidates, activate them at the right scope, and supply the compatible version to a later inquiry with provenance and exposure.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Reuse a successful method (Priority: P1)

A worker on a later question receives a tested project method skill that was
captured automatically from earlier work, including its assumptions and
failure cases.

**Why this priority**: The system only saves time if it actually supplies
the right knowledge when a related task arrives.

**Independent Test**: Capture a method in one inquiry, activate it under the
project policy, and answer a second compatible question.

**Acceptance Scenarios**:

1. **Given** a validated low-risk method, **when** activation conditions
   pass, **then** its project-scoped version becomes available with a recorded
   reason and no redundant user prompt.
2. **Given** a compatible later question, **when** the worker starts,
   **then** it receives the exact skill version and its executed recipe or
   application is recorded.

### User Story 2 - Protect authoritative rules (Priority: P1)

A proposed business definition that conflicts with an established one is
held for focused owner review. Accepting it for one analysis does not silently
replace the organization-wide rule.

**Why this priority**: Business-rule errors can contaminate many later answers.

**Independent Test**: Propose a conflicting margin definition and inspect
its status, authority, and later retrieval.

**Acceptance Scenarios**:

1. **Given** a conflicting definition, **when** activation is considered,
   **then** the conflict and required authority are visible and the proposal
   is not applied as the current rule.
2. **Given** an authorized new definition with a clear scope and effective
   period, **when** activated, **then** the old version remains traceable and
   later work selects the compatible version.
3. **Given** a configured owner explicitly approves one concrete scoped rule,
   **when** its tests and conflict check pass, **then** that approval satisfies
   authority once and the version activates without a redundant owner prompt.

### Edge Cases

- An active skill becomes stale after a source unit or access-role change.
- A skill includes a prior evaluation outcome in its references.
- A user explicitly approves a concrete rule and is its configured owner.
- A later worker sees a skill entrypoint but does not execute its example.
- A skill would require privileges the later worker does not have.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Activation MUST record candidate evidence, type, scope,
  audience, authority, tests, conflict check, version, and decision reason.
- **FR-002**: The product MUST automatically activate a project method when
  a preapproved low-risk policy applies, its scope and conflict checks pass,
  and a policy-owned validator checks its scoped example against an
  independent fixture or previously committed execution that the candidate
  author did not create for that check. Otherwise it remains `candidate`.
  Worker-authored tests alone are insufficient. New or changed authoritative
  definitions, expanded audience, access rules, or release policies MUST
  receive the configured owner's review before activation.
- **FR-003**: The product MUST accept explicit approval of a concrete,
  clearly scoped rule from its configured owner as the authority decision
  without demanding redundant approval.
- **FR-004**: A result from one study MUST NOT be placed in a general skill
  as an unscoped claim; outcome references carry provenance and exposure.
- **FR-005**: Later retrieval MUST check skill version, audience, access,
  freshness, and current status before supplying it. Source-skill
  compatibility MUST use feature 015 FR-001's normative predicate; method
  and rule skills MUST declare their own scope predicate at activation.
- **FR-006**: The inquiry MUST record supplied skill references, actually
  executed examples or recipes, and any prior-outcome exposure.
- **FR-007**: Stale or revoked skills MUST NOT silently guide current work;
  earlier inquiries retain the version they used.
- **FR-008**: Withdrawal by the configured skill authority MUST set the
  affected active version to `revoked`, prevent new supply, and retain
  historical use records. A changed source assumption without authority
  withdrawal MUST set `stale` instead.

### Key Entities

- **Skill version**: Immutable reusable instruction and its validation.
- **Activation decision**: Authority, tests, conflicts, and scope.
- **Skill use**: What entered later context and what was actually applied.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A later compatible inquiry receives and uses the validated
  method version without another request to save or recreate it.
- **SC-002**: Every seeded conflicting business rule remains `candidate` until
  the configured authority resolves it; zero prior definition versions vanish.
- **SC-003**: No stale skill or prior evaluation outcome enters a new worker
  context without a status and exposure record.
- **SC-004**: The owner-approval fixture activates exactly one tested,
  conflict-free scoped version using the recorded approval and asks for zero
  duplicate approvals.
- **SC-005**: A worker-authored self-test alone activates zero methods; an
  independently checked low-risk method activates under the frozen policy.
  Each changed definition, expanded-audience, access-rule, and release-policy
  fixture remains `candidate` until its configured owner approves it.
- **SC-006**: The authority-withdrawal fixture marks the active version
  `revoked` and supplies it to zero new inquiries; a changed-unit fixture
  marks only affected guidance `stale` while historic uses remain visible.
- **SC-007**: Each activation record contains candidate evidence, type,
  version, scope predicate, audience, authority, independent test result,
  conflict check, and decision reason; missing one blocks activation.
- **SC-008**: A method or rule skill whose role, period, source, or declared
  scope predicate mismatches the later inquiry is not supplied as current
  guidance; the compatible version is supplied with its usage recorded.
- **SC-009**: A single-study Result proposed as a general skill remains
  `candidate` with outcome provenance and exposure; it is supplied to zero
  unrelated inquiries as an authoritative unscoped rule.
- **SC-010**: A later worker that receives a skill entrypoint but executes
  none of its examples records supply without executed use; the next inquiry
  can distinguish that case from actual recipe execution.

## Assumptions

- The organization configures who owns authoritative definitions.
- The first automatic activation policy is limited to tested, project-scoped
  methods that do not alter access or required evidence policy.
- Supplying a skill does not prove a model understood every reference.

**Proposal trace**: §§13, 21; T33–T35, T39, T59; I11, I16–I17.
