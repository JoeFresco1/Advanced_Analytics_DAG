---
description: "Task list for frozen policies and structured required checks"
---

# Tasks: Frozen Policies and Structured Required Checks

**Input**: Design documents from `specs/010-run-required-checks/`

**Prerequisites**: `plan.md`, `spec.md`, `research.md`, `data-model.md`, and `contracts/required-checks-v1.md`.

**Tests**: Required by the feature's seeded fixtures and success criteria. The final acceptance task runs the feature test file plus the repository suite.

**Organization**: Tasks use the two accepted P1 stories. Shared immutable policy registration is foundational because both stories rely on it.

## Phase 1: Setup

**Purpose**: Establish synthetic-only fixture inputs for the policy and check record contracts.

- [ ] T001 [P] Create synthetic Plan, Claim, policy, validator, execution, artifact, and exact subject reference builders in `src/analytics/acceptance/010.test.ts`.

## Phase 2: Foundational Policy Contract

**Purpose**: Establish the owner-controlled, versioned policy registry required before checks can be admitted.

- [ ] T002 [P] Implement strict owner-registered immutable policy profiles, method/scope applicability, required validator descriptors, permitted `not_applicable` reasons, claim rule, authority route, schema validation, and policy-fixture gate in `src/analytics/policies.ts`.

**Checkpoint**: The current policy reference comes only from the configured owner registration route; worker proposals cannot publish or alter required validators.

## Phase 3: User Story 1 - See a failed check (Priority: P1)

**Goal**: Retain a structured failed analytical check when its validator runs successfully and finds the seeded fault, block the dependent Claim, and avoid favorable-result retries.

**Independent Test**: Inject twelve overlapping synthetic entities. The execution record shows process completion; the exact Check Result records `fail` and the count; the Claim is ineligible; and the fixture observes no retry seeking a pass. Checker crashes and malformed output remain `error`.

- [ ] T003 [US1] Add failing acceptance cases for process-success/check-fail separation, twelve-entity overlap, negative-baseline no-retry behavior, checker crash, malformed output, and tested failure-class limits in `src/analytics/acceptance/010.test.ts`.
- [ ] T004 [US1] Implement schema-validated `CheckResultV1` ingestion with the frozen fields, canonical outcomes, exact Plan/policy/validator/subject references, observed/expected values, evidence refs, tested failure classes, limits, and rationale in `src/analytics/checks.ts`.
- [ ] T005 [US1] Execute only the policy-resolved validator through 009 `executeStageV1`, retain process status separately from check outcome, record crash/schema/binding failures as `error`, and never retry a valid negative or failed check in `src/analytics/checks.ts`.

**Checkpoint**: The overlap fixture is retained as a failed check and blocks its Claim even when the command exits zero.

## Phase 4: User Story 2 - Enforce required policy (Priority: P1)

**Goal**: Make required-check coverage, parameter admission, freshness, and presentation eligibility derive from the frozen owner policy rather than the worker's submitted check list.

**Independent Test**: Omit, rename, or replace a required validator and vary each canonical result/coverage state. Only a current pass or an exact policy-justified N/A satisfies a required condition; the N/A remains distinct from pass.

- [ ] T006 [P] Implement policy-bound parameter admission and validator allowlisting in `src/analytics/policies.ts`; reject unknown/out-of-contract parameters and worker attempts to choose executables, versions, required checks, N/A allowances, approval authority, or claim rules.
- [ ] T007 [P] Implement required-check coverage and freshness evaluation in `src/analytics/checks.ts`, matching the full required set to exact Plan, policy, validator/code, and subject references and reporting omitted results as `missing` and changed bindings as `stale`.
- [ ] T008 [US2] Implement `supported_presentation_eligible` from the complete frozen truth table in `src/analytics/checks.ts`; require every condition to be current pass or policy-supported N/A and keep unsupported scope, fail, error, inconclusive, missing, stale, and unjustified N/A blocked.
- [ ] T009 [US2] Add acceptance cases for owner registration before dependent execution, unregistered/unsupported profiles, worker-relaxed or omitted/renamed validators, parameter rejection, every eligibility state, N/A reason handling, and subject/policy/validator version changes in `src/analytics/acceptance/010.test.ts`.

**Checkpoint**: Worker proposals cannot weaken policy, and any missing, stale, failed, inconclusive, error, or unsupported required condition remains visible and blocks supported presentation.

## Phase 5: Polish and Cross-Cutting Acceptance

**Purpose**: Verify the accepted 010 contract as one slice and record actual evidence without changing requirements or thresholds.

- [ ] T010 Complete the synthetic SC-001–SC-006 and proposal T02/T03/T04/T10/T23 assertions in `src/analytics/acceptance/010.test.ts`, run `pnpm build` and `pnpm test`, and write actual command results, limits, and an FR/SC-to-evidence table in `specs/010-run-required-checks/verification.md`.

## Dependencies and Execution Order

### Phase Dependencies

- **Setup**: T001 creates reusable synthetic references; it can run alongside T002 because the files differ and neither consumes the other's implementation.
- **Foundational**: T002 establishes the owner-approved policy ref. It blocks check execution and admission.
- **User Story 1**: T003 → T004 → T005. The acceptance contract is written before its implementation; record ingestion precedes stage execution.
- **User Story 2**: Starts after T005 because it consumes the structured result and execution contract. T006 and T007 may proceed in parallel after T005 because they edit separate modules; T008 consumes both; T009 adds the integrated policy and truth-table fixtures.
- **Polish**: T010 depends on all prior tasks and writes `verification.md` only after the final checks have actually run.

### User Story Dependencies

- **US1 (P1)**: Depends on the foundational owner-registered policy. Delivers retained check outcomes and blocked Claim eligibility for the overlap failure.
- **US2 (P1)**: Depends on US1's Check Result and execution binding. Enforces required policy coverage and evaluates supported presentation.

### Parallel Opportunities

- **Setup/foundation**: T001 and T002 can run concurrently because they own different files and use the frozen contract.
- **US1**: No safe parallel implementation after its tests are added; T004 and T005 extend the same `checks.ts` contract and must stay ordered.
- **US2**: After T005, T006 (`policies.ts`) and T007 (`checks.ts`) can run concurrently. T008 and T009 follow both.

## Implementation Strategy

### MVP First

Complete T001–T005 and validate US1 independently: a checker executes, records a distinct fail result on the seeded overlap, blocks the Claim, and does not retry to obtain a pass.

### Incremental Delivery

1. Register a frozen policy and its validator set.
2. Deliver the US1 executable check path and failure evidence.
3. Add US2 parameter admission, exact coverage/freshness, and truth-table evaluation.
4. Run all feature fixtures and the repository suite; record actual evidence in `verification.md`.

The feature depends on 009 for the production stage and append-only evidence interfaces. Feature 011 consumes the check trace and integration 002 proves the browser path; this task list does not move those outputs into feature 010.

## Task-to-Unit Ownership

The required manifest in `execution.json` assigns every checklist task exactly once. Its final acceptance unit owns the only verification report output. The graph-listed implementation files are assigned to units as follows: `src/analytics/policies.ts` to policy/admission work, `src/analytics/checks.ts` to check execution/coverage/eligibility, `src/analytics/acceptance/010.test.ts` to synthetic fixtures, and `specs/010-run-required-checks/verification.md` to final verification.

## Coordinator decomposition amendment (2026-10-04)

The execution manifest defines the new bounded order and acceptance commands. Test-first setup stays with its production dependency. Each intermediate unit adds executable `scope:<unit-key>:` behavioral tests with positive and failure controls; final feature acceptance remains complete. Original obligations split across new IDs are retained in `docs/build/decomposition-audit.json`; coverage maps reference their full replacement task set. Existing requirement, threshold, interface and evidence rules remain normative.
