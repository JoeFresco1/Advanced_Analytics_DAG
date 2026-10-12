# Tasks: 023 â€” Durable analysis-attempt ledger and repeat detection

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## scope-t001

- [ ] T001 Create the ledger provider scaffold and export analyticsExtensionV1 from src/analytics/ledger.ts, declaring the catalog dependency and using the existing 003 extension types.

## scope-t002-t003

- [ ] T002 [US1] Add synthetic timeline tests in src/analytics/acceptance/023.test.ts for committed controller facts, explicit missing interpretation, absent Results, and retained null, failed, and inconclusive work (write these cases before the implementation tasks in this phase).

### Implementation for User Story 1
- [ ] T003 [US1] Implement LedgerAttemptV1 projection and stable admission/execution/rationale event records in src/analytics/ledger.ts using the shared AnalyticsEventEnvelopeV1 and 009 project-memory commit/recovery interfaces.

## scope-t004

- [ ] T004 [US1] Connect 003 admission, execution, orientation-read, and finalization hooks plus the access-safe ledger show command in src/analytics/ledger.ts; preserve current grant checks, recovery, redaction, event replay, exposure refs, and missing-prose fallback.

## repeat-orientation

- [ ] T005 [US2] Add synthetic orientation tests in src/analytics/acceptance/023.test.ts for exact, related, incompatible, unresolved, paraphrased same-question, explicit rerun, and intentional replication cases (write these cases before the implementation tasks in this phase).
- [ ] T006 [US2] Import compareAnalyticalIdentityV1 from src/analytics/analytical-identity.ts in src/analytics/ledger.ts and map its dimension results to attributed exact, related, incompatible, or unresolved AnalyticalMatchV1 assessments without duplicating the identity predicate.
- [ ] T007 [US2] Implement the ledger orientation and pre-repeat rationale hooks in src/analytics/ledger.ts so match assessments reach 020 once before answer ranking, prior outcomes are not supplied by 023, changed scope is linked, and authorized replication creates a distinct purpose instance without changing the Plan.

## acceptance-verification

- [ ] T008 Complete src/analytics/acceptance/023.test.ts with T48-T51 orientation-to-timeline and remaining access, exposure, redaction, recovery, rebuild, multi-execution, retry, and changed-estimand cases; run every 023 acceptance check and write the actual FR/SC evidence and limitations to specs/023-record-analysis-ledger/verification.md.

## Dependencies

| Unit | Depends on |
| --- | --- |
| scope-t001 | Completed feature prerequisites |
| scope-t002-t003 | scope-t001 |
| scope-t004 | scope-t002-t003 |
| repeat-orientation | scope-t004 |
| acceptance-verification | repeat-orientation |
