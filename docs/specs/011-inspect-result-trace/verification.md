# Verification: Numerical report bindings and browser evidence trace

## Planning-stage status

Runtime implementation and product acceptance have **not been run** in this
task-plan-repair stage. This file records the frozen coverage and the evidence
the final implementation unit must collect; it does not claim any feature
criterion has passed. T013 owns the actual `pnpm build`, `pnpm test`, browser
acceptance, and final evidence references.

## Requirement and success coverage

| Requirement | Primary success criterion | Planned task coverage |
| --- | --- | --- |
| FR-001 | SC-009 | T007–T009 |
| FR-002 | SC-002 | T002, T004, T006 |
| FR-003 | SC-001 | T001–T006, T011, T012 |
| FR-004 | SC-006 | T007–T009 |
| FR-005 | SC-007 | T007–T009 |
| FR-006 | SC-004 | T010–T012 |
| FR-007 | SC-005 | T001, T003, T010–T012 |
| FR-008 | SC-008 | T001, T003, T010–T012 |

| Success criterion | Planned task coverage |
| --- | --- |
| SC-001 | T002–T006 |
| SC-002 | T002, T004, T006 |
| SC-003 | T003, T005, T006 |
| SC-004 | T010–T012 |
| SC-005 | T003, T010–T012 |
| SC-006 | T007–T009 |
| SC-007 | T007–T009 |
| SC-008 | T001, T003, T010–T012 |
| SC-009 | T007–T009 |
| SC-010 | T002–T004, T006 |

## Edge and proposal-case coverage

| Edge case | Owning requirement / check | Planned task coverage |
| --- | --- | --- |
| Narrative swaps metrics or rounding hides a material difference | FR-002 / SC-002 | T002–T004, T006 |
| Active markup or out-of-root path | FR-006 / SC-004 | T010–T012 |
| Reader lacks source permission | FR-007 / SC-005 | T001, T003, T010–T012 |

| Proposal case | Planned task coverage |
| --- | --- |
| T11: report swaps denominator, unit, or metric value | T002 explicitly tests metric, unit, and denominator swaps |
| T60: active markup and out-of-root paths | T010–T012 |

## Required integration evidence

- Synthetic committed provenance records cover source version, skill
  version/status, independent validation, authority decision,
  supplied-reference, executed-recipe, and outcome-exposure RecordRefV1
  relationships. T003 exercises both real JSON endpoints and browser display;
  missing/changed/restricted paths retain safe labels with exact refs and no
  raw or protected content. No 014/022 implementation is a prerequisite.
- The paired SC-008 fixtures assert both `workflow-level` and
  `independently permissioned source` labels, and zero ungranted raw values or
  restricted paths in both viewer paths (T001, T003, T010–T012).
- Every fixture chart is opened in browser acceptance. The trace exposes and
  asserts aggregation, axes/selectors/units, labels, scale/range,
  filters/population effect, uncertainty definition/encoding, and exact
  source Result; planted binding changes remain visible (T007–T009).
- The answer click-through reaches the exact committed data-version entry;
  changed/missing/redacted evidence, grant denial/revocation, markup,
  traversal, and symlink escape remain visible without unauthorized content
  (T002–T006, T010–T012).

## Final evidence to record

T013 will replace or complete this planning record with actual commands,
exit statuses, browser observations, and evidence references for every FR,
SC, edge case, T11, and T60. Browser startup or interaction failure must be
recorded as failure, never as skipped acceptance.
