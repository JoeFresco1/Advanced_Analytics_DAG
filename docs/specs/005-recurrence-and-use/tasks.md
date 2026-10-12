# Tasks: 005 â€” Slice D recurrence and consequential-use integration

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## scope-t001-t002

- [ ] T001 Define the deterministic two-window source fixture, semantic-definition variant, source positions, event/processing times, correction timing, and opaque answer-card/ledger reference inputs in `src/analytics/demo-recurrence.ts`.
- [ ] T002 Drive the synthetic scenario through the existing CLI/controller and provider hooks from 015, 016, and 017; resolve committed records and viewer links through 009/011 in `src/analytics/demo-recurrence.ts`.

## scope-t003-t004-t005

- [ ] T003 Use the 015 and 016 providers to run the same planned analysis on two distinct bounded windows, retain both Result/Claim versions, classify the same-schema changed-definition pair `incomparable`, and record the canonical comparison and reason in `src/analytics/demo-recurrence.ts`.
- [ ] T004 Render the exact window identity, provisional/final completeness, comparison interpretation limit, pinned skill version, and Plan/look exposure through the existing answer/trace route from `src/analytics/demo-recurrence.ts`.
- [ ] T005 Add focused acceptance cases for version preservation, same-schema business-meaning drift yielding `incomparable`, missing required window metadata yielding `unassessed`, provisional completeness, canonical comparison states, and repeated-look display in `src/analytics/acceptance/005.test.ts`.

**Checkpoint**: The first and second windows remain separately inspectable; an unassessed or incomparable pair cannot become an unqualified trend.

## scope-t006

- [ ] T006 Before any correction, commit the report assertion, exact Claim/Result/Check/Review references, consequential Use Assessment, and Decision through the 017 path in `src/analytics/demo-recurrence.ts`.

## scope-t007-t008-t009

- [ ] T007 Ingest a late event after that report and Decision exist, invoke only 016's correction traversal, and carry all nine impact categories—including the fixture-supplied answer-card and ledger references—into the answer/use view in `src/analytics/demo-recurrence.ts`.
- [ ] T008 Reassess through 017 after the impact is committed; show the previous assessment as `stale`, preserve its linked historical Decision and evidence, and write any current assessment/Decision as new linked records in `src/analytics/demo-recurrence.ts`.
- [ ] T009 Add acceptance cases for correction-after-consumption, two successive corrections, report/Claim/skill/use impact, all nine dependent record links, historical Decision preservation, stale authorization, and all four canonical reproduction values (`not_attempted`, `reproduced`, `mismatched`, `unavailable`) separately from availability in `src/analytics/acceptance/005.test.ts`.

**Checkpoint**: The correction is a new version; every affected reference remains auditable; authorization cannot reuse a stale assessment.

## scope-t010

- [ ] T010 Return routine `inconclusive` and `conflicting` Claims through the internal answer path as `answered_with_uncertainty`, retaining their evidence states and limitations in `src/analytics/demo-recurrence.ts`.

## scope-t011

- [ ] T011 Present the exact Claim, Result, Check, Review, policy and requested-use revisions at the focused gate; keep empty rationale `pending_review` and record reviewer, substantive rationale, unresolved issues, and any use-scoped `risk_accepted` outcome in `src/analytics/demo-recurrence.ts`.

## scope-t012-t013

- [ ] T012 Keep an internal saved/exported report separate from an external-publication request; assess the actual publication through 017 and show a policy revision staling the prior assessment in `src/analytics/demo-recurrence.ts`.
- [ ] T013 Add acceptance cases for required authorization, uncertain and conflicting internal answers, no approval from opening a trace, risk acceptance without evidence relabeling, internal export without publication authorization, and policy-change staleness in `src/analytics/acceptance/005.test.ts`.

**Checkpoint**: The same evidence may support an honest routine answer while a consequential action remains gated by its exact current use assessment.

## final-verification

- [ ] T014 Run the focused 005 acceptance test, `pnpm build`, `pnpm test`, `pnpm smoke:viewer`, and the synthetic live demo; verify every FR, SC, and edge case is exercised without skipped/TODO acceptance cases in `src/analytics/acceptance/005.test.ts`.
- [ ] T015 Record actual command outcomes, committed IDs, viewer observations, and FR/SC evidence in `specs/005-recurrence-and-use/verification.md`.

## Dependencies

| Unit | Depends on |
| --- | --- |
| scope-t001-t002 | Completed feature prerequisites |
| scope-t003-t004-t005 | scope-t001-t002 |
| scope-t006 | scope-t003-t004-t005 |
| scope-t007-t008-t009 | scope-t006 |
| scope-t010 | scope-t007-t008-t009 |
| scope-t011 | scope-t010 |
| scope-t012-t013 | scope-t011 |
| final-verification | scope-t012-t013 |

## window-view


## correction-reassessment


## Capability amendment — 2026-10-10

The execution manifest owns amended unit order and review scope. The preserved original task text remains a whole-feature obligation; split units jointly satisfy it. New corrections have their own checklist IDs.
