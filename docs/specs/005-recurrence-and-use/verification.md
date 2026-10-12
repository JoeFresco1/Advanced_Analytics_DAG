# Verification Plan: Recurrence and Consequential Use Integration

**Status**: Planned; implementation has not run in the task-plan stage.

The final `final-verification` unit replaces each planned evidence description below with the actual test assertion/result, committed record IDs, viewer observation, command and exit status. It records failures as observed; it does not change accepted thresholds, reference answers, or requirement wording.

## Commands

| Purpose | Command | Stage status |
| --- | --- | --- |
| 005 deterministic acceptance | `pnpm exec tsx --test src/analytics/acceptance/005.test.ts` | Pending implementation |
| TypeScript build | `pnpm build` | Pending implementation |
| Full repository test suite | `pnpm test` | Pending implementation |
| Existing viewer smoke | `pnpm smoke:viewer` | Pending implementation |
| Synthetic live recurrence demo (`gpt-6-luna` / `xhigh`) | `pnpm exec tsx src/analytics/demo-recurrence.ts --fixture recurrence-use-synthetic --model gpt-6-luna --reasoning xhigh` | Pending implementation |

## Functional Requirements

| Requirement | Primary accepted check | Planned acceptance assertion | Actual evidence |
| --- | --- | --- | --- |
| FR-001 | SC-001 | `recurrencePreservesPriorResultAndClaim`: distinct source/execution versions; prior Result and Claim revisions remain addressable in the viewer. | Pending implementation |
| FR-002 | SC-002 | `windowIdentityAndCompletenessVisible`: boundaries, completeness, source position, lateness, and applicable label maturity are visible. | Pending implementation |
| FR-003 | SC-002 | `comparisonNeverOverclaims`: compatible, qualified, incomparable, and unassessed fixtures use the 016 states and reasons; no unqualified change for the latter two. | Pending implementation |
| FR-004 | SC-003 | `correctionImpactLinksAllNineTypes`: late correction after report consumption links Results, Claims, Check Results, reports, answer-card refs, ledger refs, skill versions, Use Assessments, and Decision refs. | Pending implementation |
| FR-005 | SC-006 | `assessmentBindsExactRevisions`: Use Assessment names the exact Claim, Result, Check, Review, and policy revisions. | Pending implementation |
| FR-006 | SC-004 | `routineAndConsequentialUsesDiffer`: routine internal use and configured consequential/publication use follow their policy-owned routes. | Pending implementation |
| FR-007 | SC-004 | `inconclusiveAndConflictingInternalAnswersRemainHonest`: both Claim evidence states remain visible, can return a useful uncertain internal answer, and are never recast as `supported`. | Pending implementation |
| FR-008 | SC-006 | `emptyRationaleCannotAuthorize`: the trace and relevant revisions are shown; opening without substantive rationale remains `pending_review` and issues no authorization. | Pending implementation |
| FR-009 | SC-007 | `riskAcceptanceLeavesEvidenceAndExposureAlone`: use-scoped actor/rationale/uncertainty is recorded; historical Claim evidence and Plan exposure labels do not change. | Pending implementation |
| FR-010 | SC-005 | `evidenceAndPolicyChangesStaleCurrentUse`: corrected evidence and a new policy revision stale prior assessments before a linked reassessment; previous Decision remains inspectable. | Pending implementation |
| FR-011 | SC-006 | `reproductionIsIndependentOfExecutionAndAvailability`: `not_attempted`, `reproduced`, `mismatched`, and `unavailable` are shown as 009 reports them; historic execution success does not derive reproduction. | Pending implementation |

## Success Criteria

| Criterion | Planned evidence | Actual evidence |
| --- | --- | --- |
| SC-001 | Fixture version graph shows zero overwritten prior Result/Claim records and each new Result links to its exact source window. | Pending implementation |
| SC-002 | Viewer comparison panel shows all seeded definition, coverage, late-data and completeness changes before trend text. | Pending implementation |
| SC-003 | 016 impact view enumerates every known fixture Claim/Use Assessment and links the remaining seven dependency categories with current validity or linked reassessment. | Pending implementation |
| SC-004 | Consequential request without required configured authorization is blocked; routine inconclusive internal answer completes with limitation. | Pending implementation |
| SC-005 | Same correction makes the prior authorization `stale`; new assessment uses current evidence; previous Decision remains available. | Pending implementation |
| SC-006 | Viewer presents exact Claim/Result/Check/Review/policy revisions, substantive review fields, and all four reproduction fixtures independently of exit status. | Pending implementation |
| SC-007 | Risk-acceptance event records the actor and accepted uncertainty; evidence and post-observation labels have unchanged historical digests/states. | Pending implementation |
| SC-008 | Internal report save/export produces zero publication authorizations; opening a gated trace with empty rationale produces zero authorizations; an actual publication request follows configured policy. | Pending implementation |

## Edge Cases

| Edge | Accepted mapping | Planned assertion | Actual evidence |
| --- | --- | --- | --- |
| EC-001: Same schema, changed business meaning | FR-003 → SC-002 | Changed definition yields the 016-owned `incomparable` state; no unqualified trend. A separate predeclared adjustment fixture may exercise `qualified_comparison`. | Pending implementation |
| EC-002: Late event after report was consumed | FR-004 → T20 (016 primary owner) | The 005 integration receives the 016 correction impact after the original report/Decision exist and shows the affected references. T20 remains primarily owned by 016. | Pending implementation |
| EC-003: Internal answer is exported but not sent for publication | FR-006 → SC-008 | Saving/exporting the internal report creates zero external-publication authorizations; a later publication request is separately assessed. | Pending implementation |
| EC-004: Trace opened with no substantive rationale | FR-008 → SC-008 | Review remains `pending_review`; no `authorized` or `risk_accepted` assessment is written. | Pending implementation |
| EC-005: Inconclusive claim is useful internally | FR-007 → SC-004 | Inquiry may return `answered_with_uncertainty` with the Claim still `inconclusive`; exercise `conflicting` with the same evidence-preservation rule. | Pending implementation |
| EC-006: Policy changes after authorization | FR-010 → SC-005 | New policy revision stales current assessment and requires a new linked assessment; historical Decision remains. | Pending implementation |

## Viewer Evidence Required

The final report records the local viewer route and the exact displayed record IDs/revisions for: both windows and their comparison, Plan/look exposure, source-skill validity, report assertion and evidence trace, all nine correction-impact categories, old and current Use Assessments, and the preserved historical Decision. It also records the viewer principal/grant profile and any metadata withheld under that grant. The output must show the feature's actual result, including null, inconclusive, conflicting, stale, unavailable, or denied cases; it must not substitute a green execution status for an analytical judgment.
