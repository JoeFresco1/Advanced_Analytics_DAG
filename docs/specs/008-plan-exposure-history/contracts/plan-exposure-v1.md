# Plan and Exposure Contract (feature 008)

**Schema authority**: Frozen contracts in `docs/build/interfaces.md` and the generated schemas owned by feature 001. This feature adds record behavior and controller-facing adapters; it does not define a second `PlanRegistrationV1` union or edit the shared interface freeze.

## 008 facade hooks for feature 003

The feature facade exports typed request/result operations for the 003 controller to compose through its frozen lifecycle callbacks:

```ts
type PlanExposureFacadeV1 = {
  registerPlanV1(request: RegisterPlanRequestV1): Promise<RegisterPlanResultV1>;
  lockPlanV1(request: LockPlanRequestV1): Promise<LockPlanResultV1>;
  assessPlanChangeV1(
    request: AssessPlanChangeRequestV1,
  ): Promise<AssessPlanChangeResultV1>;
  commitExposureBeforeReleaseV1(
    request: ExposureReleaseRequestV1,
  ): Promise<ExposureReleaseResultV1>;
  rehydratePlanExposureV1(
    request: RehydratePlanExposureRequestV1,
  ): Promise<RehydratePlanExposureResultV1>;
};
```

```ts
type RegisterPlanRequestV1 = {
  registration: PlanRegistrationV1;
  actorRef: RecordRefV1;
};
type RegisterPlanResultV1 =
  | { status: 'registered'; plan: RegisteredPlanV1 }
  | { status: 'unsupported_profile'; profile: string; limitation: string }
  | { status: 'invalid'; issues: Array<{ path: string; reason: string }> };

type LockPlanRequestV1 = {
  planRef: RecordRefV1;
  inspectionRuleRef: RecordRefV1;
  exposureRefs: RecordRefV1[];
  lockedAt: string;
};
type LockPlanResultV1 =
  | { status: 'locked'; plan: RegisteredPlanV1 }
  | { status: 'invalid'; reason: string };

type AssessPlanChangeRequestV1 = {
  priorPlanRef: RecordRefV1;
  proposedRegistration: PlanRegistrationV1;
  workerReason: string;
  workerRef: RecordRefV1;
  codeDigest: Digest;
};
type AssessPlanChangeResultV1 = {
  status: 'assessed';
  assessment: PlanChangeAssessmentV1;
  nextPlan: RegisteredPlanV1 | null;
};

type ExposureReleaseRequestV1 = {
  exposure: ExposureRecordV1;
  releaseTargetRef: RecordRefV1;
  principalRef: RecordRefV1;
  commitId: string;
  expectedManifestRevision: number;
};
type ExposureReleasePermitV1 = {
  exposureRef: RecordRefV1;
  releaseTargetRef: RecordRefV1;
  committedManifestRef: RecordRefV1;
};
type ExposureReleaseResultV1 =
  | {
      status: 'ready';
      commitStatus: 'committed' | 'replayed';
      permit: ExposureReleasePermitV1;
    }
  | {
      status: 'blocked';
      commitStatus: 'revision_conflict' | 'rejected';
      reason: string;
    };

type RehydratePlanExposureRequestV1 = {
  history: CommittedInquiryHistoryV1;
  remainingBudgetRef: RecordRefV1;
};
type RehydratePlanExposureResultV1 = {
  status: 'rehydrated' | 'incomplete';
  context: PlanExposureContextV1;
  limitations: string[];
};
```

`assessPlanChangeV1` mechanically compares the locked analytical fields, stores the worker's reason and attribution, and returns the immutable linked revision or implementation-only Execution decision. An unresolved classification remains explicit and cannot qualify for `planned_confirmatory`.

`ExposureReleaseRequestV1` binds the inquiry and Plan refs inside the exposure record, actor, exposed material, source/partition, basis, evidence, access guarantee, exact release target, and 009 commit identity/revision. `ExposureReleaseResultV1` is a discriminated result: `ready` carries a committed exposure ref and a permit bound to that exposure and release target, and is possible only for 009 `committed` or `replayed`; `blocked` carries the `rejected` or `revision_conflict` status and reason and has no permit. The 003 caller may supply the target to a worker context or invoke a follow-on `executeStageV1` only when it receives the permit. The hook does not perform worker-context delivery.

`RehydratePlanExposureRequestV1` carries the committed inquiry-history snapshot supplied by 003 after 009 recovery. Its result contains the validated Plan/exposure projection and history-completeness/access-guarantee facts. 003 uses that result to build the actual current or replacement-worker context.

## Registration and lock

The caller supplies one registration payload and an authenticated actor. Validation is against the generated discriminated schema for `exploratory`, `descriptive`, `predictive`, or `inferential`. Unknown properties, missing required properties, invalid references, non-finite values, mismatched discriminators, and unsupported modes fail closed. The Question ref, exact source bindings, output declarations, budget ref, and required-check policy refs must resolve in the controller's authorized committed view. An acquisition recipe may be registered, but the exact acquired source version must be bound before computation.

An unlocked registration may be corrected under the same draft identity. `lockPlanV1` verifies the complete profile, registration and exposure facts, records lock time and digest, and returns a `RegisteredPlanV1`. A locked payload cannot be mutated. Any edit creates a new immutable Plan record with `predecessorRef` and a `PlanChangeAssessmentV1`.

## Plan change classification

```ts
type PlanChangeKindV1 = 'substantive' | 'implementation_only' | 'unresolved';

type PlanChangeAssessmentV1 = {
  schemaVersion: 1;
  priorPlanRef: RecordRefV1;
  nextPlanRef: RecordRefV1 | null;
  changedLockedFields: string[];
  kind: PlanChangeKindV1;
  workerReason: string;
  workerRef: RecordRefV1;
  controllerReason: string;
  assessedAt: string;
  priorExposureRefs: RecordRefV1[];
  codeDigestChanged: boolean;
  executionRef: RecordRefV1 | null;
};
```

The controller compares the locked population, target, method/comparison, partition design, search space, and stopping rule. A changed one requires a linked Plan revision and `substantive`. A worker explanation is retained but never overrides the mechanical diff. If the proposal claims code-only while one of these fields changed, admission rejects that classification and requires a substantive fork. If a code/program digest changes while those fields remain identical, record `implementation_only`, retain the Plan ref, and require a new Execution/code digest. If the semantic effect cannot be resolved, retain `unresolved`; it cannot receive `planned_confirmatory`.

## Exposure record

```ts
type ExposureBasisV1 = 'observed' | 'self_reported' | 'unknown';

type ExposureRecordV1 = {
  schemaVersion: 1;
  exposureId: string;
  inquiryRef: RecordRefV1;
  planRef: RecordRefV1;
  actorRef: RecordRefV1;
  material: string[];
  sourceOrPartitionRef: RecordRefV1 | null;
  occurredAt: string;
  basis: ExposureBasisV1;
  evidenceRefs: RecordRefV1[];
  accessGuarantee: 'enforced_boundary' | 'workflow_withheld' | 'unrestricted_or_unknown';
  reason: string;
};
```

`sourceOrPartitionRef` can be null only when no single source/partition applies; `material` and `reason` must still state the exposure scope or why the scope is unknown. A missing access event is never synthesized as “no exposure.” For a local shell-readable evaluation fixture, use `workflow_withheld` and disclose that access isolation is not enforced. Do not call it an independent holdout.

The writer commits this record through the controller's 009 publication service before the matching result, summary, or outcome-bearing skill is supplied to a worker. `observed` means the product recorded the delivery or view at its boundary; it does not prove what a local shell read outside that boundary. `self_reported` preserves the worker's statement with its attribution. `unknown` covers missing history or a boundary that cannot establish whether access occurred.

## Canonical interpretation

Only the canonical values are valid: `planned_confirmatory`, `exploratory`, `outcome_informed`, and `exposure_unknown`.

```text
deriveInterpretation(lockedPlan, inspectionRule, committedExposure, hostGuarantee):
  incomplete or unknown relevant access/history -> exposure_unknown
  no directed lock / unplanned exploratory work -> exploratory
  relevant outcome seen before the current substantive choice -> outcome_informed
  otherwise, lock + declared inspection rule before first relevant look -> planned_confirmatory
```

The last rule is scoped to the Plan's named comparison and partition. A development-informed plan can have a linked fresh confirmatory Plan only when an actual enforced boundary establishes that the worker had no access to the fresh evaluation outcome before lock. The historical development Plan remains exploratory or outcome-informed. Review approval, human risk acceptance, a shortened summary, new execution, new worker, or changed harness cannot restore any earlier label.

## Planned candidates and resamples

The candidate ID, split/partition roles, resampling scheme, seed rule, repeated-evaluation procedure, comparison family, and stopping rule are read from the locked profile. The controller admits a candidate/resample only when its exact identity belongs to that declaration and allowance. A declared candidate remains planned even after an earlier candidate result is observed. An undeclared candidate or unplanned inspection first records exposure, then requires a substantive linked Plan revision and `outcome_informed` (or `exposure_unknown` if the prior access cannot be established). A valid negative result does not invoke a favorable-result retry.

## Committed history and replacement context

```ts
type CommittedInquiryHistoryV1 = {
  schemaVersion: 1;
  inquiryRef: RecordRefV1;
  committedManifestRef: RecordRefV1;
  records: Array<{ ref: RecordRefV1; kind: string; envelope: unknown }>;
  historyComplete: boolean;
  accessGuarantee: 'enforced_boundary' | 'workflow_withheld' | 'unrestricted_or_unknown';
};

type PlanExposureContextV1 = {
  schemaVersion: 1;
  inquiryRef: RecordRefV1;
  originalWording: string;
  interpretations: Array<{ text: string; authorRef: RecordRefV1; createdAt: string }>;
  planTimeline: Array<{ planRef: RecordRefV1; interpretation: string; predecessorRef: RecordRefV1 | null }>;
  exposureTimeline: ExposureRecordV1[];
  historyComplete: boolean;
  accessGuarantee: string;
  remainingBudgetRef: RecordRefV1;
  contextDigest: Digest;
};
```

The controller supplies `CommittedInquiryHistoryV1` from the existing committed-read capability after 009 recovery establishes the current manifest head. The provider must include only references reachable from the committed manifest. 008 checks inquiry ownership, stable refs, revision/digest equality, event order, and envelope kinds before deriving labels. Corrupt, missing, redacted, or inaccessible history remains visible as a bounded limitation and produces `exposure_unknown`; it must not be repaired from a worker summary.

For a replacement worker, the controller rebuilds this context from committed records and a freshly computed remaining-budget reference. It carries the original wording, every operational interpretation, every Plan revision, all relevant Exposure refs and labels, and the context digest. The worker may receive a concise rendering in addition to this payload, but the rendering cannot replace or edit it.

## Controller-path acceptance seam

Feature 008 runs before the full inquiry controller in feature 003. Its acceptance calls the production 008 facade hooks with synthetic/local fixtures and real 009 commit/recovery and `executeStageV1` services. It tests strict registration rejection, immutable Plan transitions, the commit-before-release permit, planned search/resampling, and the rehydrated projection. A rejected/conflicting exposure commit must produce no permit and no stage invocation. The acceptance does not instantiate a fake controller or delegated worker and does not claim that a rehydration projection was delivered to a replacement worker. Feature 003 composes these same request/result hooks through its production `controller.ts`/CLI and owns the full T06/T07/T09/T61 worker-context and replacement assertions.
