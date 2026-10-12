# Tasks: 003 â€” Slice B delegated question to inspectable answer

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## extension-foundation

- [X] T001 [P] Define AnalyticsExtensionV1, typed command/context/result contracts, lifecycle hook types, the fixed module/export allowlist, capability manifest, deterministic dependency ordering, and validation errors in src/analytics/extensions.ts. Only absence of the exact optional module may become an unavailable capability; reject provider faults, missing dependencies, duplicate commands, invalid schemas, and cycles.
- [X] T002 [P] Define the worker invocation context and typed plan/code/check/report proposal envelope in src/analytics/worker.ts. Preserve original question, permitted context refs, exposure, remaining budget, capability data, model/harness settings, context digest, and attribution; do not treat opaque worker values as authoritative.
- [X] T003 Add synthetic registry and worker-boundary fixtures in src/analytics/acceptance/003.test.ts after T001 and T002. Cover valid registration, exact missing optional provider, transitive/present import failure, missing dependency, duplicate extension/command, invalid command schema, cycle, and unbound worker-authored numeric output.

**Checkpoint**: Registry capabilities and worker proposals are typed, deterministic, access-scoped, and independently testable before CLI dispatch.

## scope-t004

- [X] T004 [US1] Implement question intake and orientation in src/analytics/controller.ts. Resolve the principal and grants before constructing context; persist a Question record retaining original wording, interpretation, intended use, assumptions, and unresolved definitions; invoke the real feature 008 registration/lock and exposure APIs on the ordinary delegated path; durably record any retrieved or observed outcome exposure before supplying that material to the worker; and register/lock the predictive Plan with all required fields before directed execution. Preserve `exposure_unknown` for unknown history and `exploratory`/`outcome_informed` for early or post-observation work as applicable; none can receive `planned_confirmatory` without a locked plan and declared inspection rule preceding relevant outcome exposure.

## runtime-contract

- [X] T023 Implement the feature-local DelegatedRuntimeServicesV1 and DelegatedInquiryRequestV1 contracts plus validateDelegatedRuntimeServicesV1 in src/analytics/runtime.ts. Model trusted principal/source resolution, attributed worker invocation, pinned stage execution, committed reads/publication, viewer location, clock and cancellation as explicit typed services using the existing 001/008/009/012/013 and AnalyticsExtensionContextV1 types. Export a validation function that accepts a complete injected service set and rejects missing services or worker-supplied grants. Document the exact runtime service construction and next consumers in docs/build/003-runtime-handoff.md. No stub inquiry executor, canned answer, public command change, or new shared wire field. This boundary is independently testable with injected synthetic services; production construction belongs to runtime-composition.

## worker-invocation

- [X] T024 Implement invokeInquiryWorkerV1 in src/analytics/worker.ts using the runtime service contract and existing worker-envelope validators. Execute the configured noninteractive worker through an injected argument-vector transport with a production adapter; send the exact attributed context/requested-output digest and validate plan/code/check/report envelopes. Preserve process failure versus valid negative proposal, timeout/cancellation and unavailable telemetry. Never calculate authoritative analytical values or grant access. Test a valid envelope, malformed/unbound output, transport failure, timeout and context tampering; use synthetic transports in automated tests, leaving the live model journey to final acceptance.

## scope-t011-t012

- [X] T011 [US3] Integrate the feature 012 budget and terminal-outcome resolver in src/analytics/controller.ts. Enforce the 15-minute scheduling allowance, 12 invocations, four optional branches, three Plan revisions, and five-minute command timeout; reserve mandatory checks/reporting, distinguish scheduling cutoff from tested cancellation, and report unavailable/estimated cost accurately.
- [X] T012 [US3] Add acceptance fixtures in src/analytics/acceptance/003.test.ts for all six canonical outcomes, a valid imprecise internal answer, unresolved user definition, budget stop, required execution failure, process-exit-code changes, valid negative no-retry behavior, exhausted optional work, and missing cost telemetry.

**Checkpoint**: Bounded inquiries stop with the canonical evidence-based outcome and a readable partial answer without consuming reserved checks/finalization capacity.

## lifecycle-dispatch

- [X] T025 Implement dispatchAnalyticsCommandV1 and dispatchAnalyticsLifecycleV1 in src/analytics/extensions.ts, and controller context-service construction in src/analytics/controller.ts. Consume loadAnalyticsExtensionsV1 and its fixed registry; enforce schema/grant checks before command invocation, dependency-ordered hooks, stable event IDs, explicit optional absence and required-hook failure without completion. Bind publishRun/publishProject/recovery to the real 009 scope services. Exercise a real injected provider through these production dispatch entry points, not a helper-only simulation. Do not wait for optional future provider files or edit their fixed schemas.

## stage-admission

- [X] T006 Implement admitInquiryStageV1 in src/analytics/controller.ts. Consume orientInquiryV1, worker envelope validation, the runtime service contract, feature-012 admitProposalV1 and the registered budget. Admit one proposal only after its predecessor settles; invoke feature-009 executeStageV1 for pinned computation/check commands with generic retries disabled. Carry Question/Plan and program/environment/source refs. Test valid stage invocation, changed pins, overlapping admission, denied proposal and execution failure retaining evidence. Downstream numeric comparison, lineage and report composition have separate owners.

## stage-lineage

- [ ] T017 Implement bindInquiryLineageV1 in src/analytics/controller.ts. Bind admitted stage sources/transformations through feature 007; record joins, exclusions, row/entity counts, cutoffs and denominator scope from actual committed execution outputs. Reject or mark unsupported the specific invalid lineage per 007 without deleting the execution. Test unchanged valid scope, multiplied entities and shifted denominators using the real stage/measurement boundary.

## prediction-checks

- [ ] T018 Implement evaluateInquiryPredictionV1 in src/analytics/controller.ts. Consume admitted pinned execution and lineage; call feature-013 computeAnalytics for authoritative Python baseline/candidate comparison and feature-010 checks for split overlap, feature availability, label maturity and training-only preparation. Bind each check to exact execution/code/policy refs; preserve null/negative results with no favorable-result retry. Test valid/null controls and each assigned required-check failure before claiming supported presentation.

## report-binding

- [ ] T019 Implement bindInquiryReportV1 in src/analytics/controller.ts. Consume the committed Result and required-check records; bind every report assertion to exact Result, denominator, unit and scope via existing report/Claim services. Reject swapped values and opaque worker-only numbers; preserve unsupported and negative outcomes. Test exact valid bindings and tampered value/unit/scope through the production report binding entry point.

## scope-t008

- [ ] T008 [US2] Preserve exploration, exposure, and plan revision links in src/analytics/controller.ts and src/analytics/worker.ts. Exercise the ordinary observation path through feature 008's real exposure and plan-classification APIs: persist outcome exposure before delivering observed results to the worker; keep declared search within the locked plan; and make each post-observation substantive change a linked Plan revision with `outcome_informed` interpretation without erasing or relabeling earlier plans or exposure. Unknown history remains `exposure_unknown`; no unknown or early exposure is labeled `planned_confirmatory`.

## scope-t009

- [ ] T009 [US2] Build replacement-worker context in src/analytics/worker.ts from prior plans, results, policy, exposure, source/access scope, and remaining allowance. Through the normal controller context-supply path, durably record required feature 008 exposure before delivering exposed prior records; preserve `exposure_unknown` when history or access is uncertain and never turn it into `planned_confirmatory`; record the supplied context digest; and ensure a shortened summary cannot drop authoritative exposure or reset the budget.

## scope-t010

- [ ] T010 [US2] Implement replayable inquiry finalization and exact-check invalidation in src/analytics/controller.ts and src/analytics/acceptance/003.test.ts. Commit the run terminal intent, required project-memory records, then the run completion acknowledgement with stable IDs; resume missing writes with the same IDs and payload; verify a code or policy revision makes the earlier successful check stale for the changed execution; and after reload assert the same Question fields and links survive registration, execution, terminal commit, and inspection. Retain incomplete status on write or required-hook failure and return no completed response before durable acknowledgements.

**Checkpoint**: Outcome-informed history remains immutable across revisions/restarts, and completion is recoverable without a cross-scope transaction or an early acknowledgement.

## runtime-composition

- [ ] T026 Implement createDelegatedRuntimeV1 and runDelegatedInquiryV1 in src/analytics/runtime.ts after the worker, controller stages, budget, exposure and finalization boundaries pass. Construct actual services for the frozen registered synthetic/local source form: resolve principal/current grants through 001 before source reads; resolve source IDs against a trusted project fixture registry rather than arbitrary paths or worker-supplied authority; use existing fixture programs and authoritative 013 computation. Obtain attributed semantic interpretation and Plan proposals from the worker adapter, validate and lock through 008, run admitted stages, invoke registered extension hooks and durable finalization. Use feature-011 committed answer/trace lookup and record lookup location so inspection works after process restart. Missing configured identity, fixture or capability yields an explicit unavailable/denied result, never invented setup or a canned answer. Test the complete runtime with synthetic transport and real persistence; CLI token parsing remains T005.

## scope-t005

- [ ] T005 [US1] Implement the public dag analytics ask, answer show, and trace commands in src/analytics/cli.ts, and export `analyticsCmd(argv, cmd)` for the `analytics` top-level trampoline already registered by 001 in src/commands/index.ts; do not edit that shared registry. Parse the frozen nested command forms, invoke the controller and feature 011 viewer route, return the inquiry ID, and require no user-authored YAML or graph-node selection. T007 verifies that the live `dag analytics ask` dispatcher reaches this CLI handler.

## cli-comparison-integration

- [ ] T007 Implement production CLI/dispatcher meaningful-feature and null-feature integration fixtures in src/analytics/acceptance/003.test.ts after runtime composition and T005. Invoke the frozen question-and-fixture form without YAML/node selection. Verify estimate, interval, delta, one fixed scope, Question/Plan bindings and workflow-withheld; access isolation not enforced.

## cli-lineage-integration

- [ ] T020 Add production CLI/dispatcher fixtures for denominator multiplication/shift and customer/split overlap in src/analytics/acceptance/003.test.ts. Verify each required integrity failure retains committed evidence and blocks supported presentation with the exact denominator/lineage trace.

## cli-temporal-integration

- [ ] T021 Add production CLI/dispatcher fixtures for future features, immature labels and evaluation-fitted preparation in src/analytics/acceptance/003.test.ts. Inspect the retained failed-check trace and verify each affected claim is ineligible for supported presentation.

## cli-report-integration

- [ ] T022 Add the swapped-value production CLI fixture in src/analytics/acceptance/003.test.ts. Verify the reported number, Result, denominator, unit and scope are exact and an opaque/swapped worker value cannot become supported.

## scope-t013

- [ ] T013 Complete three recorded demo-allowance runs and source-to-answer trace fixtures in src/analytics/acceptance/003.test.ts. Verify every authoritative displayed value reaches its fixed source version, program digest, environment, Execution, Result, Plan, and transformation; preserve the stop or support state and do not exceed invocation/time limits.

## scope-t014

- [ ] T014 Complete extension lifecycle and feature 008 integration acceptance in src/analytics/acceptance/003.test.ts. Assert stable dependency-ordered hooks, ledger assessment before answer ranking, one ledger match per orientation, explicit unavailable optional capabilities, required-profile absence, duplicate commands, cycles, present-provider errors, and hook failure preventing durable completion. Through the ordinary delegated controller, assert that the real 008 lock/exposure APIs are called on both context-supply and observation paths; exposure is durable before context delivery; early analysis stays `exploratory`, unknown history stays `exposure_unknown`, and neither is labeled `planned_confirmatory`; and a post-observation substantive change creates an `outcome_informed` linked revision while retaining prior records and labels.

## scope-t015

- [ ] T015 Add explicit SC-001/SC-006/SC-008 gap assertions in src/analytics/acceptance/003.test.ts. Include a boundary predictive fixture at the exact locked delta; assert lower-bound equality is inconclusive rather than improvement and upper-bound equality is meaningful_improvement_excluded under the 013 rule, reporting estimate/interval/delta/scope. In an end-to-end production inquiry, assert original question text, interpretation, intended use, assumptions, and unresolved definitions are unchanged across Question/Plan registration, authoritative execution, terminal commit, reload, and `answer show` inspection. Assert every seeded value present only in opaque worker output appears zero times as a supported report value; assert the first-screen answer separates predictive improvement, causal influence, and a negative/non-improvement result and explicitly says non-improvement does not prove no relationship.

## scope-t016

- [ ] T016 Run pnpm test, record the live synthetic demo with gpt-6-luna/xhigh as described in specs/003-delegated-inquiry/quickstart.md, and write specs/003-delegated-inquiry/verification.md with actual evidence for every FR-001..FR-014, SC-001..SC-009, and EC-001..EC-007. Report any failure or limitation directly; do not alter accepted specs, checklists, policy thresholds, or reference answers.

## Dependencies

| Unit | Depends on |
| --- | --- |
| extension-foundation | Completed feature prerequisites |
| scope-t004 | extension-foundation |
| runtime-contract | scope-t004 |
| worker-invocation | runtime-contract |
| scope-t011-t012 | worker-invocation |
| lifecycle-dispatch | scope-t011-t012 |
| stage-admission | lifecycle-dispatch |
| stage-lineage | stage-admission |
| prediction-checks | stage-lineage |
| report-binding | prediction-checks |
| scope-t008 | report-binding |
| scope-t009 | scope-t008 |
| scope-t010 | scope-t009 |
| runtime-composition | scope-t010 |
| scope-t005 | runtime-composition |
| cli-comparison-integration | scope-t005 |
| cli-lineage-integration | cli-comparison-integration |
| cli-temporal-integration | cli-lineage-integration |
| cli-report-integration | cli-temporal-integration |
| scope-t013 | cli-report-integration |
| scope-t014 | scope-t013 |
| scope-t015 | scope-t014 |
| scope-t016 | scope-t015 |

## measurement-binding

- [ ] T027 Complete bindInquiryLineageV1 measurement binding against the admitted pinned measurement request and actual authoritative descriptive/predictive outputs. Require exact denominator definition ref, row/entity count basis, unit, population/period/group and declared measurement scope, plus committed artifact identity; matching totals alone are insufficient. Reject wrong definition, shifted scope, wrong unit/count basis and worker-authored fabricated diagnostics while retaining the execution. Include a genuinely valid real-calculator control and source-to-report trace. This unit jointly completes the original T017 obligation; do not change its final CLI fault checks.

## prediction-integrity-checks

- [ ] T028 Complete original T018 by binding and invoking the existing 010 integrity-check producers for split/customer overlap, feature availability, label maturity and training-only preparation. Bind every observation to exact stage/execution/Result/code/policy refs. Test valid controls and each failed required check through evaluateInquiryPredictionV1; retain all failures and make supported presentation ineligible. Consume authoritative comparison results from prediction-checks without recomputing them in TypeScript.

## Capability amendment — 2026-10-10

The execution manifest owns amended unit order and review scope. The preserved original task text remains a whole-feature obligation; split units jointly satisfy it. New corrections have their own checklist IDs.

## Canonical decomposition: finalization-invalidation

- [ ] T029 Complete the check-invalidation portion of original T010: exact code or policy revision makes the earlier check stale for the changed execution. Retain the old check and execution; exercise real 010 check identity and ordinary controller reload. This supplements T010; do not rebuild finalization.

## Canonical decomposition: exposure-integration-acceptance

- [ ] T030 Complete original T014 exposure integration through the ordinary controller and real 008 APIs: context and observation exposure committed before delivery; exploratory/unknown labels remain honest; post-observation substantive changes create outcome_informed linked revisions without relabeling earlier history.

## Canonical decomposition: question-roundtrip-acceptance

- [ ] T031 Complete original T015 Question persistence: original text, interpretation, intended use, assumptions and unresolved definitions survive registration, execution, terminal commit, reload and answer-show. Compare exact production record fields, not fabricated fixtures.

## Canonical decomposition: report-honesty-acceptance

- [ ] T032 Complete original T015 report honesty: opaque worker-only values appear zero times as supported values; first-screen answer distinguishes prediction, causal influence and non-improvement, and states that non-improvement does not prove no relationship. Inspect the real committed answer/viewer.
