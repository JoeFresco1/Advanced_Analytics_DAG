# Feature Specification: Bound an Autonomous Inquiry

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Let a worker propose useful analytical steps while the controller enforces work, resource, and stopping limits.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Delegate without managing a task graph (Priority: P1)

An analyst asks a question; the worker proposes and executes bounded next
steps while preserving the question, prior evidence, and remaining allowance.

**Why this priority**: The user expects the worker to do the analytical labor.

**Independent Test**: Submit an inquiry that needs exploration followed by
one directed comparison, and inspect admitted actions and outcomes.

**Acceptance Scenarios**:

1. **Given** a valid next step within budget, **when** proposed, **then** the
   controller records its purpose, dependency, expected output, and cost
   before admitting it.
2. **Given** a repeated no-progress proposal, **when** reviewed, **then** no
   unbounded loop is admitted.

### User Story 2 - Receive a useful stop (Priority: P1)

An analyst gets a readable answer or partial answer when time, access, or
evidence runs out.

**Why this priority**: A settled task graph alone does not answer a question.

**Independent Test**: Exhaust a fixture allowance during optional exploration
and inspect the final state without an extra worker invocation.

**Acceptance Scenarios**:

1. **Given** exhausted optional work, **when** work stops, **then** required
   evidence already completed and remaining gaps are reported.
2. **Given** unavailable token telemetry, **when** limits are shown,
   **then** exact monetary cost is not claimed while enforceable limits remain.

### Edge Cases

- An active task continues after a scheduling deadline.
- Nested parallel computation exceeds reserved resources.
- A software runner retries a valid negative result.
- A replacement harness starts without prior exposure context.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Every inquiry MUST have finite time, invocation, branch, and
  revision allowances with an explicit stop reason.
- **FR-002**: The controller MUST admit worker proposals only after validating
  question relevance, dependencies, expected output, cost, and acyclicity.
- **FR-003**: The controller MUST reserve capacity for mandatory validation
  and reporting before optional exploration.
- **FR-004**: A valid negative or inconclusive result MUST NOT be retried as a
  process failure; repairs and substantive method changes require new records.
- **FR-005**: The product MUST distinguish a scheduling cutoff from active
  cancellation and advertise only limits it actually enforces.
- **FR-006**: Each worker invocation MUST receive prior plans, results,
  exposure, policy, and remaining allowance.
- **FR-007**: Inquiry completion MUST distinguish `answered`,
  `answered_with_uncertainty`, `insufficient_evidence`, `needs_input`,
  `budget_exhausted`, and `execution_blocked` under the canonical transition
  rules; task exit status MUST NOT substitute for the inquiry outcome. The
  worker MUST propose its semantic answer and reason with evidence references;
  the controller MUST verify required fields and apply the canonical
  precedence rule, preserving the attributed semantic judgment.
- **FR-008**: A proposed branch is no-progress when its analytical identity
  exactly matches a prior attempt under 023 FR-006 and it offers no new
  observation, justified method change, or recorded replication purpose.
  Unknown identity dimensions MUST yield an unresolved match, not an automatic
  rejection. The controller MUST reject a proven no-progress proposal and
  record the matching prior attempt; intentional replication with rationale
  MAY proceed within budget.
- **FR-009**: For semantic admissions such as question relevance and
  answer usefulness, the worker MUST supply a reason and cited evidence.
  Deterministic admission MUST verify identity, references, budget, and
  policy only. A disputed semantic assessment MUST be attributed to a named
  reviewer or remain unresolved; it MUST NOT be represented as mechanically
  proven by the controller.
- **FR-010**: The first local fixture profile MUST enforce at most 15 minutes
  of admitted work, 12 worker invocations, four optional branches, three Plan
  revisions, and a five-minute timeout for each command. The controller MUST
  probe or test the host's active-process cancellation before advertising a
  hard wall-time cap; otherwise it MUST describe only a scheduling cutoff.
  Token, monetary, memory, and source-byte caps MUST be labeled estimates
  unless their adapter reports an enforceable mechanism.
- **FR-011**: A proposal that launches nested parallel work MUST declare its
  aggregate CPU, worker, and source-query demand. The controller MUST admit
  it only against the remaining shared reservation, not once per child. If
  the host cannot enforce a requested hard resource cap, admission MUST
  refuse work that requires that guarantee or disclose the weaker profile.

### Key Entities

- **Inquiry**: Question and current domain outcome.
- **Proposal**: A bounded candidate next action.
- **Allowance**: Reserved and consumed work or resources.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: The looping fixture terminates within its declared limits and
  admits zero repeated no-progress actions.
- **SC-002**: The exhausted-budget fixture still returns completed evidence
  and a readable stop reason without another model call.
- **SC-003**: No missing-telemetry fixture advertises an exact cost cap it
  cannot observe or enforce.
- **SC-004**: A replacement worker receives the prior plan, result, exposure,
  policy, and remaining allowance; zero prior exposure or budget is reset.
- **SC-005**: Six frozen stopping fixtures produce the six canonical outcomes
  under the recorded precedence rule, including an imprecise useful answer,
  an unresolved user definition, exhausted budget, and required execution
  failure; changing only the process exit code changes none of the outcomes.
- **SC-006**: An exact repeat with no new purpose is rejected; a changed
  denominator and an unknown input version are not falsely called exact
  repeats. An intentional replication with rationale is admitted within its
  allowance.
- **SC-007**: The fixture rejects the thirteenth worker invocation, fifth
  optional branch, and fourth Plan revision. A command exceeding five
  minutes is terminated under the tested process timeout. A host with no
  active cancellation advertises a scheduling cutoff, not a hard 15-minute
  termination guarantee.
- **SC-008**: A proposed action with a missing expected output, unresolved
  dependency, cycle, or unaffordable cost is not admitted. Optional
  exploration cannot consume the reserved validation and final-reporting
  allowance; disputed relevance retains a worker or reviewer reason rather
  than a mechanical proof label.
- **SC-009**: Two nested children whose combined declared demand exceeds
  the remaining reservation are not launched together; a hard-cap-required
  request with no host enforcement is rejected rather than labeled enforced.
- **SC-010**: A valid negative outcome produces zero automatic process
  retries; a code repair creates a new Execution and a changed method creates
  a new Plan revision, each linked to its predecessor and reason.

## Assumptions

- Resource and cost enforcement depend on the actual harness and host.
- A dry estimate informs admission but does not guarantee final spend.

**Proposal trace**: §§4, 10, 16; T04, T12, T26–T27; I04, I08, I10, I13.
