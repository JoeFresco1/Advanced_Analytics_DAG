# Verification: Find Past Analytical Answers

**Status**: Planned handoff only — implementation acceptance has not run.  
**Owner**: Final implementation unit `acceptance-verification` (T009).  
**Product checks run in this planning stage**: None. All implementation task checkboxes remain open. Replace this file's pending entries with actual command output, fixture evidence, live-worker refs, and limitations only after implementation.

## Frozen acceptance targets

- 99 mixed CSV/workbook files: find the intended answer using question and period without a filename; do not fabricate provenance or promote intermediate artifacts to answer cards.
- At least 150 cards and 20 annotated queries: Hit@3 ≥ 18/20, computed as qualifying queries divided by 20, with pins disabled; each returned card has a relevance class and reason.
- Five no-match queries, including an empty corpus: zero false direct answers; restricted cards do not appear.
- Later live worker in the same synthetic/local project: relevant prior work surfaces with no user hint; the worker's use/rejection and exposure remain recorded.
- 023 prior-attempt match call count is exactly one; 020 retrieval event count is exactly one for the orientation case.
- Durable finalization, missing-summary fallback, curation/index rebuild, project home, and ledger-timeline link pass without changing Claim state/scope or accepted thresholds.

## Planned commands

```text
pnpm build
pnpm test
```

T009 also runs the two-inquiry live-recall procedure in [quickstart.md](quickstart.md) with `gpt-6-luna` / `xhigh`. Record the actual fixture/project/source refs, harness and version, context/source digests, inquiry/card/retrieval IDs, budget, exposure, worker disposition, command outcome, and any local workflow-boundary limitation. Keep evaluator labels and reference answers out of worker context.

## Functional requirement evidence map

| Requirement | Planned task(s) | Actual evidence |
| --- | --- | --- |
| FR-001 | T001, T002, T008, T009 | Pending |
| FR-002 | T003, T004 | Pending |
| FR-003 | T003, T004 | Pending |
| FR-004 | T005, T006 | Pending |
| FR-005 | T005, T006, T009 | Pending |
| FR-006 | T007, T008, T009 | Pending |
| FR-007 | T003, T004, T007, T008, T009 | Pending |
| FR-008 | T001, T002, T003, T004 | Pending |
| FR-009 | T001, T002, T008, T009 | Pending |
| FR-010 | T003, T007, T008, T009 | Pending |
| FR-011 | T007, T008, T009 | Pending |
| FR-012 | T005, T006, T009 | Pending |
| FR-013 | T001, T002, T005, T009 | Pending |
| FR-014 | T003, T004, T007, T008, T009 | Pending |
| FR-015 | T003, T004, T009 | Pending |
| FR-016 | T005, T006, T009 | Pending |
| FR-017 | T007, T008, T009 | Pending |

## Success criterion evidence map

| Criterion | Planned task(s) | Actual evidence |
| --- | --- | --- |
| SC-001 | T003, T004, T009 | Pending |
| SC-002 | T001, T002, T004, T009 | Pending |
| SC-003 | T005, T006, T009 | Pending |
| SC-004 | T003, T004, T007, T008, T009 | Pending |
| SC-005 | T001, T002, T003, T004, T009 | Pending |
| SC-006 | T001, T002, T009 | Pending |
| SC-007 | T007, T008, T009 | Pending |
| SC-008 | T001, T002, T005, T009 | Pending |
| SC-009 | T003, T004, T009 | Pending |
| SC-010 | T003, T004, T009 | Pending |
| SC-011 | T005, T006, T009 | Pending |
| SC-012 | T007, T008, T009 | Pending |
| SC-013 | T003, T004, T009 | Pending |
| SC-014 | T006, T007, T008, T009 | Pending |
| SC-015 | T001, T002, T008, T009 | Pending |

## Edge-case evidence map

| Edge | Scenario | Planned task(s) | Actual evidence |
| --- | --- | --- | --- |
| EC-001 | Several competing Claims | T001, T002 | Pending |
| EC-002 | Evidence redacted or unavailable | T005, T006, T009 | Pending |
| EC-003 | Prior holdout result retrieved into a new inquiry | T007, T008, T009 | Pending |
| EC-004 | Same metric, different cohorts | T003, T007, T008 | Pending |
| EC-005 | Worker exits/crashes after commit before prose | T001, T002, T009 | Pending |
| EC-006 | Many related cards compete for bounded context | T003, T004, T007, T009 | Pending |

## Proposal cases owned by 020

T31 (negative/inconclusive search), T35 (retrieved evaluation exposure), T36 (pinned card made stale), T39 (restricted result absent), T40 (remember-this-analysis pin), T41 (worker-without-summary fallback), T42 (automatic later recall), T56 (150/20 Hit@3), and T57 (empty/five-query no-match) map to implementation tasks T001-T009 as recorded in `tasks.md` and `execution.json`. Proposal IDs are not feature task IDs.

## Review rejection coverage

- **Duplicate 023 match**: T007/T008 and SC-012 assert consumption of the prior 023 orientation handoff, exactly one 023 match, and no 020 matcher call.
- **Remember-this-analysis pin**: T005/T006 and T009 exercise the explicit finalization intent and durable curation event without creating a skill or altering evidence/scope.
- **FR-013 ledger timeline**: T001/T002/T005/T009 verify a stable answer-to-ledger link and the visible admitted-attempt history.
- **Later live-worker recall**: T009 and the Quickstart require a second `gpt-6-luna`/`xhigh` inquiry in the same synthetic project, without a user hint, with actual use/exposure evidence recorded.
