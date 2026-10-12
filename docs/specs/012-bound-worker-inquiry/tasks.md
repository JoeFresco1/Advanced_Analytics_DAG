# Tasks: Bounded Worker Admission, Resources and Honest Stopping

**Input**: Design documents in specs/012-bound-worker-inquiry/

**Prerequisites**: plan.md, spec.md, research.md, data-model.md, contracts/admission-and-stopping.md, quickstart.md

**Test scope**: The specification requires frozen measurable fixtures; implement and run them in src/analytics/acceptance/012.test.ts. All tasks remain unchecked during planning.

## Phase 1: Setup

The TypeScript workspace and test runner already exist. No setup task changes package manifests, locks, shared architecture or runner code.

## Phase 2: Foundational Contracts

**Purpose**: Establish the one analytical identity comparison and the shared finite allowance types that every story consumes.

- [ ] T001 [P] Define AttemptIdentityV1 and compareAnalyticalIdentityV1 in src/analytics/analytical-identity.ts; compare every required 023 identity dimension and return exact only when all are known and equal.
- [ ] T002 [P] Define canonical inquiry budget defaults, resource/telemetry status types and shared reserve accounting in src/analytics/budget.ts; preserve unknown cost as estimated or unavailable rather than zero.
- [ ] T003 Add initial synthetic identity and budget fixtures in src/analytics/acceptance/012.test.ts after T001 and T002.

**Foundation verification**: pnpm test passes with tests for known-equal, changed-denominator and unknown-input-version identity dimensions plus the frozen count/time defaults.

## Phase 3: User Story 1 - Delegate without managing a task graph (Priority: P1)

**Goal**: Admit only typed, relevant, affordable, acyclic proposals; preserve inquiry context and decision history; reserve nested work and prohibit accidental repeats.

**Independent Test**: Submit synthetic exploration followed by a directed comparison. Inspect purpose, dependencies, expected outputs, cost/resource declaration, worker semantic reason, reservation, identity references and the durable admission event before the dispatch spy launches.

- [ ] T004 [US1] Implement typed proposal validation and controller context packet construction in src/analytics/inquiry.ts, including question/Plan refs, prior Plans/Results/exposure, policy, accessible evidence refs, graph revision and remaining allowance; test missing outputs, dependencies, cycles, unaffordable proposals and attributed/disputed relevance in src/analytics/acceptance/012.test.ts.
- [ ] T005 [US1] Apply the shared compareAnalyticalIdentityV1 predicate for no-progress decisions in src/analytics/inquiry.ts; test exact repeats, changed denominators, unknown input versions and reasoned replication in src/analytics/acceptance/012.test.ts, keeping unknown identity unresolved without rejecting a proposal on identity uncertainty alone.
- [ ] T006 [US1] Persist ProposalAdmissionRecordV1 through the feature 009 run-event commit service before returning admitted in src/analytics/inquiry.ts; test commit-before-dispatch ordering and commit-failure/no-launch behavior in src/analytics/acceptance/012.test.ts.
- [ ] T007 [US1] Reserve required checks and final reporting before optional work, and aggregate nested CPU/worker/source-query demand against the shared remainder in src/analytics/budget.ts; test combined child demand, optional-reserve protection and hard-cap refusal in src/analytics/acceptance/012.test.ts.
- [ ] T008 [US1] Classify infrastructure attempts separately from analytical outcomes in src/analytics/inquiry.ts; retain failures, keep generic runner retries at zero, and require linked new Executions for explicit infrastructure retries or code repairs and a new Plan revision for a substantive method change; test a negative baseline with zero automatic retries in src/analytics/acceptance/012.test.ts.

**Checkpoint**: The valid sequence is durably admitted before launch; an exact repeat without new purpose cannot loop; changed/unknown dimensions retain their correct comparison state; optional work cannot consume check/finalization reserves.

## Phase 4: User Story 2 - Receive a useful stop (Priority: P1)

**Goal**: Produce a canonical terminal inquiry outcome, readable partial status and truthful work/cancellation labels even when budget or prose generation ends.

**Independent Test**: Exhaust optional exploration with committed checks and inspect terminal state, remaining gaps and stop reason without an extra worker invocation; also vary only process exit status across the six frozen stop fixtures.

- [ ] T009 [US2] Implement answer-proposal validation, canonical outcome precedence and deterministic missing-prose status rendering in src/analytics/inquiry.ts; test all six terminal outcomes, exit-code independence and missing final prose in src/analytics/acceptance/012.test.ts while preserving semantic attribution.
- [ ] T010 [US2] Enforce and label the 15-minute, 12-invocation, four-branch, three-Plan-revision and five-minute command limits in src/analytics/budget.ts; test count limits, missing cost telemetry, a controlled process timeout through the host cancellation adapter, and active-cancellation versus scheduling-cutoff behavior in src/analytics/acceptance/012.test.ts.
- [ ] T011 [US2] Complete synthetic feature acceptance fixtures in src/analytics/acceptance/012.test.ts for every FR, SC and edge case, including SC-009 hard-cap refusal, SC-006 changed denominator and unknown input version, negative-baseline no-retry behavior, and persist-before-dispatch plus commit-failure/no-launch ordering.

**Checkpoint**: All six canonical outcomes, negative baseline, exhausted work, missing prose, replacement context, cancellation/cutoff and nested resource cases settle with retained evidence and no favorable-result retries.

## Phase 5: Polish and Verification

**Purpose**: Record actual acceptance evidence against the immutable feature contract.

- [ ] T012 Run pnpm test and write specs/012-bound-worker-inquiry/verification.md with actual evidence for every FR-001..FR-011, SC-001..SC-010 and EC-001..EC-004. Do not alter accepted specs, quality checklists, policy thresholds or reference answers.

## Dependencies and Execution Order

### Cross-feature prerequisites

- Feature 008 supplies immutable Plan revisions and exposure context.
- Feature 009 supplies atomic run-event/evidence persistence, recovery and one-stage execution.
- Feature 010 supplies frozen required-check policy and exact check results.
- The existing graph adds these dependencies; this feature does not edit it.

### Local unit dependencies

1. Foundation (T001-T003): no local unit dependency. T001 and T002 are separate-file work and may be done in parallel; T003 depends on both.
2. Admission (T004-T008): depends on Foundation and is serialized because inquiry, budget and acceptance fixtures share contracts.
3. Honest stopping (T009-T012): depends on Admission and runs final acceptance before writing verification.md.

| Task | Depends on |
| --- | --- |
| T001 | None |
| T002 | None |
| T003 | T001, T002 |
| T004 | T001, T002, T003 |
| T005 | T001, T003, T004 |
| T006 | T004, T005 |
| T007 | T002, T004 |
| T008 | T004, T006 |
| T009 | T004, T006, T007, T008 |
| T010 | T002, T007 |
| T011 | T001-T010 |
| T012 | T011 |

### Parallel opportunities

- Only T001 and T002 are independently parallelizable.
- Later tasks share inquiry/budget interfaces or the single acceptance file and remain serialized by execution unit.

### Suggested MVP scope

User Story 1 is the first implementation checkpoint: bounded, persisted admission and finite reservations. It is not feature-complete until User Story 2 and the full verification report are complete.

## Requirement and Acceptance Coverage

| Contract items | Assigned tasks |
| --- | --- |
| FR-001, FR-010 | T002, T010 |
| FR-002, FR-006, FR-009 | T004, T006 |
| FR-003, FR-011 | T007 |
| FR-004 | T008 |
| FR-005 | T010 |
| FR-007 | T009 |
| FR-008 | T001, T005 |
| SC-001 | T005, T011 |
| SC-002 | T007, T009, T011 |
| SC-003 | T002, T011 |
| SC-004 | T004, T011 |
| SC-005 | T009, T011 |
| SC-006 | T001, T005, T011 |
| SC-007 | T002, T010, T011 |
| SC-008 | T004, T006, T007, T011 |
| SC-009 | T007, T011 |
| SC-010 | T008, T011 |
| EC-001: active task after scheduling deadline | T010, T011 |
| EC-002: nested parallel computation | T007, T011 |
| EC-003: runner retries valid negative result | T008, T011 |
| EC-004: replacement harness lacks prior exposure context | T004, T011 |

## Coordinator decomposition amendment (2026-10-04)

The execution manifest defines the new bounded order and acceptance commands. Test-first setup stays with its production dependency. Each intermediate unit adds executable `scope:<unit-key>:` behavioral tests with positive and failure controls; final feature acceptance remains complete. Original obligations split across new IDs are retained in `docs/build/decomposition-audit.json`; coverage maps reference their full replacement task set. Existing requirement, threshold, interface and evidence rules remain normative.
