---

description: "Task list for Execute and Inspect Evidence"
---

# Tasks: Execute and Inspect Evidence

**Input**: Design documents in specs/002-executable-evidence: plan.md, research.md, data-model.md, contracts/evidence-demo-v1.md, and quickstart.md.

**Prerequisites**: Graph nodes 009, 010, 011, and 18-bootstrap; their existing dependency on 001 supplies the neutral execution adapter, Pydantic contracts, generated schemas, and synthetic fixture registry.

**Organization**: Tasks are grouped by the three accepted P1 user stories. Each task has an exact output path. Acceptance tests are explicit because this feature requires executable scenarios and the build task requires end-to-end acceptance. Story tests precede their implementation tasks.

## Phase 1: Setup

**Purpose**: Establish the direct, bounded demo command and deterministic fixture producer.

- [ ] T001 [P] Create the scenario argument interface and isolated synthetic run lifecycle in src/analytics/demo-evidence.ts.
- [ ] T002 [P] Implement deterministic synthetic input generation and fixed calculation output in python/analytics_worker/demo_evidence.py using the existing Pydantic computation contracts.

## Phase 2: Foundational

**Purpose**: Connect the fixture to the frozen production stage boundary and prepare the executable acceptance harness.

- [ ] T003 Add the scenario harness in src/analytics/acceptance/002.test.ts with isolated sidecars, 18-bootstrap fixture inputs, a `stage-call` acceptance case, and a test-local CDP helper that launches an installed Edge/Chrome through `DAG_ANALYTICS_BROWSER_PATH` or documented platform locations and fails rather than skipping when a real browser is unavailable.
- [ ] T004 Bind the immutable schema-valid Plan and exact computation inputs to executeStageV1 in src/analytics/demo-evidence.ts, with argument-vector execution and declared outputs.

**Checkpoint**: After `pnpm build`, run `node --test dist/analytics/acceptance/002.test.js`. At this checkpoint the acceptance file contains the `stage-call` case, which invokes the synthetic producer through 009 `executeStageV1` using the frozen typed Plan and verifies the returned execution/output binding; no run-file mutation or private runner exists.

## Phase 3: User Story 1 - Inspect a measured result (Priority: P1)

**Goal**: Open the fixed result and follow its exact Plan, program/environment, source, transformation, output, check, and scope references.

**Independent Test**: Run the pass scenario through executeStageV1 and commitEvidence, then open its report assertion through 011 and follow it in the browser to current matching evidence.

### Acceptance tests

- [ ] T005 [US1] Use the test-local CDP helper to open the real 011 browser route for the pass scenario, select each displayed value, and assert its complete current evidence trace in src/analytics/acceptance/002.test.ts.

### Implementation

- [ ] T006 [US1] Publish the complete pass result through commitEvidence and bind its report assertion to the exact result selector in src/analytics/demo-evidence.ts.

**Checkpoint**: The pass scenario has exactly one committed logical result and a user can reach every required evidence link from the visible value.

## Phase 4: User Story 2 - See what a check actually found (Priority: P1)

**Goal**: Retain negative results and failed/error checks while blocking supported presentation whenever frozen required-check coverage is not satisfied.

**Independent Test**: Run overlap-fail, null-improvement, malformed-check, missing-output, changed-artifact, omitted/stale-check, and unsupported-not-applicable variants; inspect the exact observation and eligibility in the viewer and confirm no method retry is admitted.

### Acceptance tests

- [ ] T007 [US2] Run overlap, null-improvement, malformed/crashed, unsupported not_applicable, omitted/stale-check, missing-output, and changed-artifact cases through the production controller and open each affected report in the real 011 browser route in src/analytics/acceptance/002.test.ts.
- [ ] T008 [US2] Assert the browser-visible check observation, exact subject, policy/validator refs, tested failure limits, canonical outcome, and dependent-claim eligibility in src/analytics/acceptance/002.test.ts without reading process logs.

### Implementation

- [ ] T009 [P] Add overlap-fail, null-improvement, malformed-check, checker-exit, and missing-output fixture behavior in python/analytics_worker/demo_evidence.py.
- [ ] T010 [P] Route exact fixture outputs through 010's frozen policy and eligibility and 011's report/viewer in src/analytics/demo-evidence.ts; retain missing/changed evidence without supported promotion.

**Checkpoint**: Check process completion stays distinct from outcome; valid negative results and failed checks remain committed; none is retried to obtain a favorable result.

## Phase 5: User Story 3 - Recover an interrupted publication (Priority: P1)

**Goal**: Recover only committed evidence, make replay idempotent, arbitrate concurrent publishers, and preserve access/availability limits in the viewer.

**Independent Test**: Interrupt before commit, lose a post-commit acknowledgement, race two expected-revision commits, and remove/restrict evidence; recover and open the resulting trace.

### Acceptance tests

- [ ] T011 [US3] Test pre-commit recovery, lost-ack replay, concurrent publication, redacted/inaccessible evidence, and semantic reproduction through committed 009/010 records opened in the real 011 browser route in src/analytics/acceptance/002.test.ts.

### Implementation

- [ ] T012 [US3] Implement pre-commit recovery, same-ID acknowledgement replay, and distinct concurrent commits at one expected revision through 009 in src/analytics/demo-evidence.ts.
- [ ] T013 [US3] Add denied-reader and redacted/unavailable evidence scenarios using current 011 viewer access checks in src/analytics/demo-evidence.ts.
- [ ] T014 [US3] Add byte-different but canonically equivalent reproduction using the declared 009 comparison rule in src/analytics/demo-evidence.ts.

**Checkpoint**: Recovery yields no partial or duplicate result; the losing publisher has a revision conflict; the viewer discloses only currently permitted metadata.

## Phase 6: Polish and Cross-Cutting Acceptance

**Purpose**: Run all criteria and record actual command/browser evidence.

- [ ] T015 Run the complete feature acceptance matrix and write the exact demo/test commands, actual command output and exit codes, committed revisions, browser name/version, 011 route, observed DOM/trace evidence (or captured browser automation trace), and any environment limitation to specs/002-executable-evidence/verification.md.

## Requirement and proposal trace

The task IDs are feature-local implementation IDs. Proposal cases below are separate IDs and remain assigned to their focused primary owners; 002 records only integrated evidence from the fixed user path.

- FR-001: T004,T005,T006; FR-002: T007,T009,T010; FR-003: T007-T010; FR-004: T007,T009,T010; FR-005: T007,T008,T009; FR-006: T005-T007,T009; FR-007: T011,T012; FR-008: T011,T014; FR-009: T007,T009,T011,T013; FR-010: T011,T013.
- SC-001: T005,T006,T009; SC-002: T007,T009; SC-003: T007,T008; SC-004: T011,T012; SC-005: T007,T010; SC-006: T011,T014; SC-007: T004-T006; SC-008: T007,T009,T010.
- EC-001 (zero exit/no output): T007-T009; EC-002 (malformed/crashed checker): T007-T009; EC-003 (unsupported not_applicable): T007,T009; EC-004 (concurrent publication): T011,T012; EC-005 (byte serialization difference): T011,T014; EC-006 (deleted/redacted evidence): T011,T013.
- Integrated proposal cases exercised by this fixed caller: T01, T02, T03, T04, T10, T13, T14, T15, T22, and T52. T23 and T60 are verified by the focused 010/011 suites run by the final acceptance commands; these labels do not replace or transfer focused-feature ownership.

## Dependencies and execution order

### Phase and unit dependencies

- foundation: T001-T004; no local-unit dependency, but graph prerequisites 009/010/011/18-bootstrap must already be complete.
- result-and-checks: T005-T010; depends on foundation because these tasks use its fixed producer and stage caller.
- recovery-and-access: T011-T014; depends on result-and-checks because recovery and access cases need committed result/check records.
- acceptance-report: T015; depends on recovery-and-access and runs all feature acceptance before writing verification.md.

### User-story dependencies

- User Story 1 follows shared setup/foundation and establishes the pass-path identities.
- User Story 2 follows the pass-path setup so failures can be compared against the same frozen input and policy; it does not weaken or replace the pass policy.
- User Story 3 follows committed result/check records from User Stories 1 and 2; isolated scenario roots keep recovery tests repeatable.
- The final acceptance unit follows all three stories and cannot mark a task complete from a planned command or unexecuted browser route.

### Parallel opportunities and examples

- Setup example: run T001 and T002 in parallel; they own separate files and have no dependency on each other's incomplete work.
- User Story 1 example: author T005 first, confirm it fails against the incomplete pass path, then implement T006. The test and production behavior share one integration contract, so there is no safe parallel pair within this story.
- User Story 2 example: author T007 and T008 first and confirm the seeded cases fail, then implement T009 and T010 in parallel because the Python fixture and TypeScript facade are separate files.
- User Story 3 example: author T011 first and confirm the recovery/access cases fail, then implement T012, T013, and T014 sequentially because all three extend the same demo module. The recovery and access behaviors must be inspected together through the viewer.
- No feature units run in parallel because they share the integration files and execution.json serializes units in listed order.

## Verification per story

- User Story 1: `pnpm test` must use the test-local CDP harness to open the real 011 route, follow the pass result in the browser, and show a complete current trace whose fixed Plan/program/environment/source/output identities match committed records.
- User Story 2: `pnpm test` must show overlap observations, valid no-gain result, malformed/missing/stale and unsupported-N/A outcomes distinctly in the browser; dependent eligibility is false where required and automatic attempt count for favorable-result seeking is zero.
- User Story 3: `pnpm test` must show no partial publication, one replayed logical result, one winning CAS commit plus one revision_conflict, and access-safe unavailable/redacted responses in the browser.
- Cross-cutting acceptance: uv run --project python pytest -q, node dist/analytics/demo-evidence.js --scenario matrix, and python scripts/audit_spec_coverage.py all exit 0; the `002.test.ts` CDP acceptance opens the actual 011 browser route and fails if the configured/discovered Edge or Chrome cannot start. verification.md records both the real demo command and the observed browser result.

## Implementation strategy

MVP is the pass path in User Story 1 after the shared typed fixture/stage foundation. Add check failure and negative-result behavior next, then recovery/access. Finish with the entire matrix and real browser evidence. Each stage consumes focused owner APIs; it does not wait for 003/007 or add a second execution path.

## Checklist format validation

All 15 implementation tasks use unchecked boxes and unique sequential T001-T015 identifiers. [P] appears only on independent tasks; [US1]/[US2]/[US3] labels appear on user-story tasks; each task description names an exact repository path. Phase 1/2 and cross-cutting tasks have no story label. No implementation task is marked complete.


## Coordinator decomposition amendment (2026-10-04)

The execution manifest defines the new bounded order and acceptance commands. Test-first setup stays with its production dependency. Each intermediate unit adds executable `scope:<unit-key>:` behavioral tests with positive and failure controls; final feature acceptance remains complete. Original obligations split across new IDs are retained in `docs/build/decomposition-audit.json`; coverage maps reference their full replacement task set. Existing requirement, threshold, interface and evidence rules remain normative.
