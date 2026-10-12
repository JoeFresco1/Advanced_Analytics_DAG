# Recurrence V1 Contract

This feature contract elaborates the frozen contracts in `docs/build/interfaces.md`; it adds no competing machine-state vocabulary and does not edit the shared freeze.

## Provider registration

`src/analytics/recurrence.ts` exports `analyticsExtensionV1: AnalyticsExtensionV1` with `schemaVersion: 1`, `extensionId: "recurrence"`, and `dependsOn: ["source-reuse"]`. It uses the 003 fixed allowlist loader and hook order. No dynamic import based on source data is allowed.

The provider uses the existing hooks as follows:

1. `orient`: read the authorized current/previous window, compatible source-skill refs and inspection policy; add bounded period/completeness/lateness context and exposure refs.
2. `afterExecution`: append the observed inspection-look record, check it against the locked policy, and return exposure/current interpretation refs before an answer is finalized.
3. `afterEvidenceCommit`: identify a new source/window revision or correction, compute the complete dependency impact, commit correction and impact refs through 009, and return committed refs.
4. `beforeFinalize`: verify required recurrence/impact writes are acknowledged and return answer/trace references for the final record. Required hook errors halt finalization under 003 semantics.

Hook outcomes use `CommandResultV1`/the frozen hook-result shape, authenticated context, and exact committed refs. Provider output is bounded; it cannot promote unsupported evidence, revise grants/policy/budget, or write committed files itself.

## Wire shapes

```ts
type PeriodComparisonStateV1 =
  | "unassessed"
  | "comparable"
  | "qualified_comparison"
  | "incomparable";

type WindowVersionV1 = {
  schemaVersion: 1;
  windowVersionId: string;
  windowKey: string;
  sequence: number;
  identityRef: RecordRefV1;
  sourceRef: RecordRefV1;
  executionRef: RecordRefV1;
  predecessorWindowRef: RecordRefV1 | null;
  correctsWindowRef: RecordRefV1 | null;
};

type DependencyImpactV1 = {
  schemaVersion: 1;
  impactId: string;
  correctionRef: RecordRefV1;
  changedFromRef: RecordRefV1;
  changedToRef: RecordRefV1;
  affected: Array<{
    recordType:
      | "result" | "claim" | "check_result" | "report" | "answer_card"
      | "ledger_entry" | "source_skill_version" | "use_assessment"
      | "decision_reference";
    recordRef: RecordRefV1;
    pathRefs: RecordRefV1[];
    currentValidity: "reassessment_required";
    reassessmentRef: RecordRefV1 | null;
  }>;
  committedImpactRef: RecordRefV1;
};
```

The full `WindowIdentityV1`, `PeriodComparisonV1`, `InspectionPolicyV1`, `InspectionLookV1`, `CorrectionRelationshipV1`, and `BoundDependencyV1` fields and invariants are specified in [data-model.md](../data-model.md). Every timestamp is timezone-qualified. Source positions, completeness, lateness, and applicable label maturity are required identity dimensions; missing required facts use the canonical `unknown` value and yield `unassessed`, never a positive match.

## State and mutation rules

- Period state and current validity use the canonical values in `docs/specs/state-vocabularies.md` exactly.
- Any new bound revision marks every reachable dependent `reassessment_required` before any semantic review. No materiality or result-direction shortcut may skip this.
- Old records are never edited into new meanings. A correction is a new source/window/execution version and a linked relationship; reassessment creates another linked record.
- A provisional window is inspectable, but its report must surface completeness and lateness limits. A missing required comparison component yields `unassessed`.
- An undeclared look preserves exposure and exploratory interpretation; an infrastructure retry does not count as a favorable analytical result.

## Persistence and compatibility

Run and project writes call 009's frozen `commitEvidence` and `commitProjectMemory` primitives with stable IDs, expected revision, exact refs, and content digest. The run record is committed before project records reference it. Replay is idempotent only for the same ID and content; revision conflicts do not overwrite. No `dag.run.json` fields, 003 registry edits, 011 viewer edits, 015 compatibility predicate edits, or new store are introduced.
