# Implementation Plan: Recurrence and Consequential Use Integration

**Branch**: `005-recurrence-and-use` | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from `specs/005-recurrence-and-use/spec.md`

## Summary

Compose the production source-skill reuse path (015/004), recurring-window and dependency-invalidation transitions (016), claim-use review and Decision transitions (017), committed evidence readers (009), and the existing answer/trace viewer (011, transitively available through 004 → 003 → 002). The synthetic demo establishes a first window, comparison and consumed report, records a consequential-use assessment and Decision, then applies a late correction and displays the resulting reassessment chain. It preserves earlier evidence and the historical Decision, creates a linked current assessment, and shows repeated-look exposure in the viewer. Feature-owned transitions and canonical vocabularies remain with their owners.

The graph does not make 020 or 023 prerequisites. 005 therefore does not load those providers, create answer-card or ledger records, classify retrieval, or assess analytical identity. It consumes the stable answer-card and ledger-entry references returned in the 016 correction-impact fixture as opaque committed references and verifies that the integration view links them. The fixture records are synthetic test inputs; their production and orientation behavior remain owned by 020/023. This keeps the 005 executable path within the frozen graph while exercising every dependent-record type required by 016 FR-004 and 005 SC-003.

## Technical Context

**Language/Version**: TypeScript 5.6, Node.js 20 or newer.

**Primary Dependencies**: Existing analytics extension registry and provider contracts; providers owned by 015, 016, and 017; committed-record and project-memory readers/writers owned by 009; answer and trace viewer owned by 011. No new package dependency.

**Storage**: Existing append-only run records under `<run>.d/analytics/` and project memory under `<project>/.dag-analytics/`, committed with 009's existing IDs, manifest revisions, and compare-and-swap flow. No state is added to `dag.run.json` and no second persistence mechanism is introduced.

**Testing**: Node's built-in test runner through the repository's `pnpm test` script, a focused `pnpm exec tsx --test src/analytics/acceptance/005.test.ts` scenario, `pnpm build`, and `pnpm smoke:viewer`. The acceptance uses synthetic/local fixtures only.

**Target Platform**: Existing local analytics CLI/controller and `viewer/analytics.html` path on Node.js 20 or newer.

**Project Type**: TypeScript analytics CLI/controller integration with a browser answer and evidence-trace viewer.

**Performance Goals**: Keep the demo within the existing bounded inquiry allowance. No new latency or throughput target is specified by the feature contract.

**Constraints**: Preserve accepted specs, checklist, policy, and reference answers. Reuse 015/016/017 transitions without defining parallel states. Preserve exact revisions, evidence bindings, exposure, and append-only history. Do not edit shared viewer/controller files or package manifests. Demonstrations use synthetic/local fixtures. 020/023 behavior is not a runtime prerequisite for this feature; stable references to their record types are opaque integration links supplied by the synthetic impact fixture.

**Scale/Scope**: One bounded recurrence scenario over two fixed windows, a same-schema business-definition change, a consumed report, a late correction (including a second correction), source-skill validity, one consequential-use review and its reassessment, and a repeated-look exposure shown in the viewer. Continuous streaming and general-purpose use policy authoring are out of scope.

## Constitution Check

| Gate | Status | Plan evidence |
| --- | --- | --- |
| Honest answer and retained uncertainty | PASS | Routine internal `inconclusive` and `conflicting` Claims remain unchanged and may finish as `answered_with_uncertainty`; they are not promoted to `supported`. |
| Execution, evidence, use, and Decision are separate | PASS | The demo links exact Claim, Result, Check, Review, policy, Use Assessment, and Decision revisions. A successful command does not authorize use. |
| Historical records and exposure remain immutable | PASS | The initial window, report, Claim, assessment, Decision, and look history remain inspectable after correction. New revisions or linked assessments carry changed state. |
| Scoped project memory and skill validity | PASS | 015 supplies the compatible skill version and its lifecycle; 016 supplies invalidation; 005 neither changes skill scope nor writes answer-card/ledger records. |
| Bounded and accurate guarantees | PASS | The demo uses the configured fixture allowance and describes local access as workflow-level when the host does not enforce isolation. |
| End-to-end producer, consumer, viewer, and failure | PASS | 015/016/017 produce owned transitions, the controller consumes them, 011 presents the answer and evidence links, and acceptance injects a post-report late correction and empty-rationale review. |
| Frozen graph and file ownership | PASS | 005 writes only its graph-owned demo, acceptance test, and feature planning artifacts. Node 011 owns the viewer; 020/023 own card/ledger production. |

No constitution exception or unresolved clarification is required.

## Design Decisions

1. **Use the existing lifecycle extensions.** The demo invokes the source-reuse, recurrence, and use-authorization providers in their declared dependency order. It does not add controller registration or lifecycle code.
2. **Persist through existing commit facades.** Domain records and references use 009's commit and recovery mechanisms. Changed evidence produces linked records; the original record bytes and IDs remain addressable.
3. **Keep state ownership with the focused features.** Use `docs/specs/state-vocabularies.md` values as-is: period comparison `unassessed`, `comparable`, `qualified_comparison`, `incomparable`; current validity `current`, `reassessment_required`, `superseded`; reproduction `not_attempted`, `reproduced`, `mismatched`, `unavailable`; Claim evidence `supported`, `unsupported`, `inconclusive`, `conflicting`; plan interpretation `planned_confirmatory`, `exploratory`, `outcome_informed`, `exposure_unknown`; and authorization `not_assessed`, `pending_review`, `authorized`, `risk_accepted`, `denied`, `stale`, `revoked`.
4. **Establish the use record before correction.** The fixture first commits the Claim/trace and a 017-owned assessment and Decision. Only then does it apply the late correction. This makes the stale transition causal and testable against an existing historical Decision.
5. **Consume card and ledger links without importing their producers.** The correction-impact fixture carries stable references for all nine 016 affected-record types. 005 resolves and displays permitted link metadata through the frozen committed-read/viewer path. It does not write `AnswerCardV1` or `LedgerAttemptV1`, perform card ranking, or call 023 match logic. The actual producers remain outside this feature's dependency closure.
6. **Keep publication distinct from saving an internal report.** A saved/exported internal report is not a publication request. An actual requested publication goes through 017 policy assessment and cannot reuse the internal assessment.
7. **Exercise both inconclusive and conflicting routine answers.** Each remains an honest internal answer under 005 FR-007. A consequential use of negative or conflicting evidence follows 017's policy gate and can proceed only under an explicit permitted `risk_accepted` assessment.

## Project Structure

### Documentation (this feature)

```text
specs/005-recurrence-and-use/
├── plan.md
├── research.md
├── data-model.md
├── contracts/
│   └── recurrence-use-integration.md
├── quickstart.md
├── tasks.md
├── execution.json
└── verification.md
```

### Source Code (repository root)

```text
src/analytics/
├── demo-recurrence.ts
└── acceptance/
    └── 005.test.ts
```

**Structure Decision**: Keep feature behavior in the graph-owned TypeScript demo and acceptance test. The demo composes existing analytics providers and records; the test drives that production path and inspects the 011 viewer. No new generic models, provider, sidecar, public endpoint, or package is introduced.

## Dependency and Integration Boundary

- Graph prerequisites are exactly 004, 016, and 017. 004 reaches viewer feature 011 transitively through 003 and 002, so the viewer is available without changing the graph.
- 004 supplies the live second-inquiry/source-skill path. Feature 015 owns compatibility and freshness, including a skill becoming `stale` when its relevant meaning changes.
- 016 owns window identity, period comparison, repeated-look exposure, correction traversal, and current-validity transitions.
- 017 owns review, authorization, staleness/revocation, risk acceptance, and Decision records.
- 009 owns durable commits and recovery; 011 owns HTTP/viewer presentation and access checks.
- 020/023 are not prerequisites in the frozen graph. Tests use synthetic committed record references for their record categories only; no 005 code imports those providers or claims their write/retrieval behavior. This integration tests 016's producer and 011's link presentation under the current graph.

## Verification Strategy

The acceptance test runs the same synthetic scenario through the production extension and committed-record path. It checks exact version links, comparison reasons, skill compatibility, the nine-category correction impact, report and reproduction states, pre-correction Decision preservation, stale then reassessed authorization, and viewer-visible exposure. Failure fixtures cover same-schema semantic drift, a late correction after report consumption, a second correction, retention loss, inconclusive and conflicting internal Claims, an empty review rationale, internal export without publication, and policy change after authorization. The final unit runs the focused test, `pnpm build`, `pnpm test`, and the repository viewer smoke check, then replaces the planned evidence slots in `verification.md` with actual command and assertion evidence.

## Post-Design Constitution Check

PASS. The design has a real producer/consumer/viewer path, keeps all domain transitions with their owners, preserves historical and exposure records, records null/conflicting states honestly, and confines changes to the graph-owned outputs. No thresholds, reference answers, access guarantees, or accepted requirements are altered.
