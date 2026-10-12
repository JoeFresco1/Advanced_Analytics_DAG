# Frozen interfaces and file ownership

**Status:** version-1 contract freeze for the existing build graph  
**Date:** 2026-09-28  
**Purpose:** names, wire fields, owners, and failure outcomes that parallel feature nodes consume. `graph.json` remains the implementation-file authority.

## Contract rules

1. JSON contracts use UTF-8, camelCase field names, `schemaVersion: 1`, and reject unknown fields at authoritative ingress. Required missing values fail closed. Optional facts are absent or explicitly `null`; `unknown` is reserved for the normative state-vocabulary sentinel, never treated as a value that supports identity.
2. A reference is a stable record ID plus revision and SHA-256 content digest. A filename or mutable path alone is never an evidence identity.
3. Pydantic models are the single normative Plan and computation wire-schema source. Shared conformance vectors are checked by Python and TypeScript. TypeScript validates schemas generated from Pydantic; it does not maintain a separate handwritten schema. Numeric calculations live in the authoritative Python calculator; controller policy consumes those values and applies the locked decision rule without fitting a second statistical model.
4. Canonical domain states come from [state-vocabularies.md](../specs/state-vocabularies.md). This file freezes names and fields, not a second state vocabulary.
5. A schema change is a new version or a backward-compatible optional extension with a recorded migration. Existing committed records and historical versions are not rewritten.

## Stable identifiers and references

```ts
type Digest = `sha256:${string}`;
type RecordRefV1 = {
  recordId: string;
  revision: number;
  digest: Digest;
};
type EvidenceRef = RecordRefV1 & {
  artifactId: string;
};
type ProducedArtifactV1 = {
  artifactId: string;
  relativePath: string;
  digest: Digest;
  mediaType: string;
  sizeBytes: number;
};
```

`RecordRefV1` is used for Question, Plan, policy, program, environment, source, transformation, Result, Claim, Check Result, review, skill, card, ledger, and commit records. `EvidenceRef` adds a stable artifact identity and canonical availability. A reference to bytes still requires current access authorization before metadata or content is returned.

## Neutral execution package boundary

Node 001 owns the complete `packages/neutral-execution/` source tree, its manifest/build exports, neutral conformance vectors, and legacy software adapter. Its local release archive and digest manifest live in `artifacts/neutral-execution/`. These are explicit graph outputs. Other nodes import the package and cannot edit a second runner. The original repository is the read-only legacy comparison target.

The package exports `NeutralTaskV1`, `NeutralRunOptionsV1`, `NeutralExecutionV1`, `runTasksV1`, `readExecutionsV1`, `stopRunV1`, and `recoverRunV1`. A task declares ID, dependency IDs, executable, argument vector, working directory, allowed output root, timeout, silence limit, and environment overrides. Run options declare run-file identity, concurrency, retry count, maximum attempts, review/repair rounds, fallback chain and final-review policy. Execution records carry task/attempt IDs, start/end times, process status/failure kind, exit code, invocation digest, stdout/stderr log references and produced paths. This is execution metadata, never analytical support.

`runner-adapter.ts` is the sole analytics caller of `runTasksV1`. It sets retry count to 0, maximum attempts to 1, review/repair rounds to 0, fallback chain to empty and final review to off, and rejects contradictory caller overrides before launch. It invokes executable/arguments without shell interpolation. `stopRunV1` and `recoverRunV1` retain cancellation/interruption history; neither resubmits analytical work. Any classified infrastructure re-execution is admitted by the controller with a new execution ID under the existing analytical attempt and budget. Node 001 tests this mechanically, including a failed required check that cannot enter rejection/repair paths.

## Plan registration wire schema

Node 001 owns strict, extra-forbidding Pydantic models in `python/analytics_worker/contracts.py`, generated JSON schemas under `src/analytics/schemas/`, and Python/TypeScript conformance vectors under `python/tests/contract-vectors/`. Node 008 owns registration, locking and exposure interpretation; it consumes these generated schemas rather than defining a second Plan vocabulary. Schema validators reject unknown discriminators, missing required fields, extra fields and non-finite numeric values at registration.

The discriminated `PlanRegistrationV1` union consists of `ExploratoryPlanV1`, `DescriptivePlanV1`, `PredictivePlanV1`, and `InferentialPlanV1`; `profile` is respectively `exploratory`, `descriptive`, `predictive`, or `inferential`. A profile selects a schema, not a support/review state. Causal and other unsupported profiles return an explicit unsupported-profile limitation; no generic model silently accepts them.

| Model/field group | Required fields and validation |
| --- | --- |
| Common registration | `schemaVersion: 1`, `planId`, positive `revision`, `questionRef`, `predecessorRef` (reference or null), `authorRef`, nonempty `purpose`, `sourceBindings`, `outputs` and `budgetRef`. Each output declares ID, relative path, role, retention and positive maximum bytes. |
| `SourcePlanBindingV1` | Discriminator `kind: version` with `sourceRef` and declared role, or `kind: acquisition_recipe` with immutable `recipeRef`, source ID and declared role. Acquisition is allowed at registration; an exact acquired version is mandatory in execution binding before computation. No mutable 'latest' reference suffices. |
| `DirectedPlanFieldsV1` | `populationRef`, `targetRef` (estimand or prediction target), `comparison` with ID/definition/baseline refs, `methodRef`, `partitionDesign`, `searchSpace`, `stoppingRule`, `requiredCheckPolicyRef`, `intendedUse`, `measurementRefs`, `exposureBoundary` and `uncertainty`. References/definitions are nonempty; a not-applicable design component requires a policy-owned reason, never omission. |
| `ExploratoryPlanV1` | Only the common registration fields are required; it has no invented target, significance procedure or confirmatory label. Supporting a directed Claim requires a separate directed registration. |
| `DescriptivePlanV1` | Common and directed fields; `measurements` specify column/definition ref, statistic (`count`, `sum`, `mean`, `proportion`), units, denominator definition, missingness/exclusion rule, grouping definitions and population/period. `uncertainty.kind: none` records a reason when only an observed summary is claimed. |
| `PredictivePlanV1` | Common and directed fields plus baseline/candidate procedures and feature definitions, prediction time, feature availability, target horizon, label maturity, population/evaluation scope, split and tuning separation, locked search candidates, primary metric/units, favorable direction, nonnegative `delta`, interval method/level, resampling unit and scheme, seed rule, and repeated-evaluation procedure. Interval level is strictly between 0 and 1. |
| `InferentialPlanV1` | Common and directed fields plus `methodProfile: two_group_renewal_rate_difference`, customer observation unit, exactly two predeclared group definitions, renewal measurement/denominator, period, estimand direction/units, practical margin/context, sampling/missingness/dependence assumptions, reduction or cluster-aware method, interval method/level, comparison family/multiplicity rule and inspection/sequential rule. Unsupported interval/dependence combinations remain limitations. |

Node 008 wraps a valid registration in `RegisteredPlanV1`: `planRef`, exact registration payload, registration time, optional lock time, frozen exposure refs and canonical `plan_interpretation`. The controller alone assigns the interpretation. Registration payload and digest are immutable after lock; substantive edits create a linked revision, while a code-only correction changes execution/code identity. Unknown exposure never becomes 'no exposure'. Node 001 supplies schema conformance for all four profiles; nodes 008/013/006 own their operational registration and method tests.

## Computation wire protocol

The Python package accepts one JSON `AnalyticsRequestV1` and returns one `AnalyticsResultV1`; the request is saved with the execution record and passed by reference to the process rather than interpolated into a shell command. The process writes result JSON to its declared output path and keeps stdout/stderr for execution diagnostics. An invalid or missing envelope is an evidence error even when the process exits zero.

```ts
type AnalyticsModeV1 = 'descriptive' | 'predictive' | 'inferential';

type ComputationBindingV1 = {
  runId: string;
  inquiryId: string;
  attemptId: string;
  executionId: string;
  planRef: RecordRefV1;
  sourceRefs: RecordRefV1[];
  transformationRefs: RecordRefV1[];
  programDigest: Digest;
  pythonVersion: string;
  dependencyLockDigest: Digest;
  environmentDigest: Digest;
  seed: number | string | null;
};

type AnalyticsRequestV1 = {
  schemaVersion: 1;
  requestId: string;
  mode: AnalyticsModeV1;
  binding: ComputationBindingV1;
  payload: unknown; // validated by the Pydantic contract for `mode`
  outputs: Array<{
    artifactId: string;
    relativePath: string;
    role: ArtifactRoleV1;
    retention: RetentionClassV1;
    maxBytes: number;
  }>;
};

type AnalyticsResultV1 = {
  schemaVersion: 1;
  requestId: string;
  mode: AnalyticsModeV1;
  binding: ComputationBindingV1; // exact echo; controller compares all fields
  payload: unknown; // validated by the Pydantic contract for `mode`
  producedArtifacts: ProducedArtifactV1[]; // staged output; commit creates EvidenceRefs
  completedAt: string;
};
```

Each mode has a strict Pydantic request and result payload: `DescriptiveRequestV1`/`DescriptiveResultV1`, `PredictiveRequestV1`/`PredictiveResultV1`, and `InferentialRequestV1`/`InferentialResultV1`. The descriptive payload carries named measurements with units, denominators, population and period scope; the predictive payload carries the planned baseline/candidate, metric and favorable direction, `delta`, interval, and the canonical predictive classification; the inferential payload carries the locked estimand, customer observation unit, predeclared groups/period, estimate, interval method/level, denominator, and assumptions. `payload` is not accepted as arbitrary JSON: the mode validator rejects mismatched discriminators, extra keys, missing required dimensions, non-finite numbers, and invalid units/states.

Node 001 bootstraps the common envelope, mode-specific Pydantic models, generated schemas, conformance vectors, Python package, dispatcher and lock before registration in 008. Node 007 owns `python/analytics_worker/descriptive.py` and `src/analytics/descriptive.ts`, including count/sum/mean/proportion with explicit denominators, exclusions and grouping; it is the descriptive calculation consumer of 007 measurement lineage. Node 013 owns `python/analytics_worker/predictive.py` and `src/analytics/predictive.ts`. Node 006 owns `python/analytics_worker/inferential.py` and `src/analytics/inferential.ts` and imports the frozen common binding/model. Later nodes do not edit `pyproject.toml` or `uv.lock`; they consume the predeclared dependencies owned by 001. The generated JSON schema snapshots and conformance vectors are checked in with 001; changing a model requires an owner revision and regeneration against the shared vectors.

Node 001's `python/analytics_worker/__main__.py` exposes `python -m analytics_worker --request <path> --result <path>`, loads the validated request, dispatches by mode to the corresponding calculator module, and validates/writes the result envelope. A module not yet built returns an explicit unsupported-mode error; it never emits dummy values. Each calculator exports `compute(request) -> AnalyticsResultV1`. Each TypeScript mode facade exports `computeAnalytics(request, services) -> Promise<AnalyticsResultV1>` and delegates invocation/binding checks to the shared adapter; it never implements numeric calculation in TypeScript.

The authoritative task invocation is a neutral execution task with explicit argument-vector semantics, bounded timeout, declared input/output references, and zero generic retries. The TypeScript facade constructs the request and reads the typed result; Python alone computes the numeric payload. Controller checks that the exact binding round-trips, output digests match, requested outputs remain beneath the execution root, and applicable frozen policy checks have current subjects.

## Frozen domain contracts

### Identity and access

| Name | Frozen fields and meaning | Owner |
| --- | --- | --- |
| `Principal` | `principalId`, `authenticationMethod`, `roles: string[]`, `projectId`. Roles are configuration-derived; a worker cannot add itself. | 001 `principals.ts` |
| `SourceGrant` | `grantId`, `principalId` or `roleId`, `sourceId`, allowed `operations` (`metadata`, `aggregate`, `row`, `sheet`, `export`, `administration`), `audience`, `effectiveFrom`, `effectiveUntil`, `ownerRef`, `revision`. Missing grant denies. | 001 `principals.ts` |
| `AttemptIdentityV1` | Known-or-unknown dimensions: source version refs and source roles, population ref, denominator-definition ref, target/estimand ref, ordered transformation refs, method/profile version, program/code digest, canonical parameter digest, and evaluation-contract digest. | 012 `analytical-identity.ts` |
| `AnalyticalMatchV1` | `schemaVersion`, `priorAttemptRef`, `status: exact\|related\|incompatible\|unresolved`, compared dimension outcomes, question-relevance proposer/reason/evidence refs, and match rationale. Unknown required identity dimension cannot be exact. | 023 `ledger.ts` |

`compareAnalyticalIdentityV1(left, right)` is the only comparison of `AttemptIdentityV1` dimensions. It reports equal, different, or unresolved per required dimension. It returns exact identity only when all required dimensions are known and equal. In 012, unresolved identity does not by itself reject a proposal. In 023, the ledger applies the worker/reviewer-attributed semantic same-question judgment and canonical match classification around that shared comparison; an unknown dimension stays `unresolved`.

### Evidence, validation, and commit

| Name | Frozen fields and meaning | Owner |
| --- | --- | --- |
| `CheckResultV1` | `schemaVersion`, `checkId`, `executionId`, `planRef`, `policyRef`, validator ID/version/code ref, exact subject refs, `outcome`, observed/expected values, evidence refs, tested failure classes/limits, and reason. Outcomes are the canonical `pass`, `fail`, `inconclusive`, `not_applicable`, `error`. | 010 `checks.ts` |
| `CommitRequestV1` | `schemaVersion`, stable `commitId`, `runId`, `expectedManifestRevision`, principal ref, execution/attempt refs, immutable records/artifact refs, validation decision refs, and commit timestamp. | 009 `publication.ts` |
| `CommitResultV1` | `schemaVersion`, `commitId`, `status: committed\|replayed\|revision_conflict\|rejected`, committed/current revision, committed manifest ref when present, and a bounded reason. A conflict never overwrites. | 009 `publication.ts` |
| `RunManifestV1` | `schemaVersion`, `runId`, `revision`, `previousRevision`, `commitId`, committed record/artifact/event refs, and digest. The current manifest pointer is the reader visibility boundary. | 009 `store.ts` |
| `ProjectManifestV1` | `schemaVersion`, `projectId`, `revision`, `previousRevision`, `commitId`, committed record/event refs, stable card/artifact/skill IDs, and scope/availability projection refs. | 009 `store.ts` owns the manifest/CAS mechanism; 019 and each memory facade own their domain records. |
| `ProjectCommitRequestV1` | `schemaVersion`, stable `commitId`, `projectId`, `expectedManifestRevision`, principal ref, immutable records/event refs, optional committed run-manifest ref and timestamp. All cross-run refs resolve to committed history; access is checked for write and referenced data. | 009 `publication.ts` |
| `ProjectCommitResultV1` | `schemaVersion`, `commitId`, canonical commit status, committed/current revision, committed project manifest ref when present, and bounded reason. | 009 `publication.ts` |
| `RecoveryReportV1` | Scope ID/kind, committed head revision/ref, replayed commit IDs, ignored staging/orphan IDs, unresolved/corrupt refs and safe cleanup actions. Never treats incomplete files as evidence. | 009 `publication.ts` |
| `AnalyticsEventEnvelopeV1` | `schemaVersion`, `streamId`, monotone `sequence`, `eventId`, `kind`, `actorRef`, `occurredAt`, payload record ref, payload digest, and prior-event digest. Envelopes are append-only; orphan events are not promoted without a committed manifest ref. | 009 `store.ts` for run stream; project event types are emitted by their domain owner and use this envelope. |

`commitEvidence(request): Promise<CommitResultV1>` and `recoverEvidence(runId): Promise<RecoveryReportV1>` own run evidence. `commitProjectMemory(request): Promise<ProjectCommitResultV1>` and `recoverProjectMemory(projectId): Promise<RecoveryReportV1>` own project persistence. Node 009 implements both using the same append-only/CAS primitive with distinct scope locks/revision streams; node 019 composes catalog records, not a second transaction engine. All ledger, answer-card, skill, import and curation writers call the project primitive. Same commit ID and same canonical request digest return the original committed revision; ID reuse with changed content is rejected. A new ID with a stale expected revision returns `revision_conflict`. No replay can replace history.

There is no atomic transaction across the run and project scopes. Finalization first commits the run result/terminal-intent record, then project memory referencing that committed revision, then an append-only run completion acknowledgement referencing the committed project revision. Commit IDs derive from inquiry, finalization ID and scope, making each step replayable. The inquiry is not returned as completed until all required memory hooks acknowledge durable writes. Crash recovery resumes the missing step with the same ID and payload; conflicts require re-reading the current manifest and a new attributed commit, never overwriting. Unacknowledged completion intent remains visible as incomplete. Future retrieval sees only committed project references, and an orphan project write with an unavailable run ref fails validation. Node 003 owns this finalization coordinator; nodes 020/023 supply the domain memory records through the extension seam below.

### Project memory and report

| Name | Frozen fields and meaning | Owner |
| --- | --- | --- |
| `ArtifactRecordV1` | Stable artifact ID, content digest, media type, canonical role (`input_extract`, `intermediate_table`, `plot`, `model`, `diagnostic`, `result_table`, `report`, `other`, `unknown_imported`), retention class (`scratch`, `cache`, `evidence`, `deliverable`), origin, producer/import ref, availability, access scope, and usage links. | 019 `catalog.ts` |
| `AnswerCardV1` | Stable card ID/revision, inquiry ref, original question, scoped answer/non-answer, key Claim/Result refs, period/population, inquiry/evidence/current-validity states, uncertainty/limitations, artifact refs, and durable curation refs. Missing interpretation is explicit; fallback never invents a finding. | 020 `answers.ts` |
| `RetrievalEventV1` | Requesting inquiry, query and structured scope, access-filtered candidate IDs, ranked cards, direct/related/not-relevant reasons, card/current evidence versions, supplied refs, exposure, and worker use/rejection rationale. | 020 `retrieval.ts` |
| `LedgerAttemptV1` | One admitted comparison purpose under a Plan revision, exact identity, append-only event refs, linked executions/results/checks/claims, explicit missing/null/failure status, worker rationale provenance, and current projection. | 023 `ledger.ts` |

Imported files without execution provenance use `unknown_imported`/unknown origin and are not answer cards by implication. Duplicate bytes do not erase distinct file paths or artifact IDs. Sensitive row values do not enter FTS summaries by default.

## TypeScript service facades

The signatures below are stable names; implementations remain in their assigned graph files.

| API | Contract | Owner |
| --- | --- | --- |
| `requireGrant(principal, sourceId, operation, audience?)` | Returns the current matching `SourceGrant`; throws/returns a typed `access_denied` outcome when identity or grant is absent, expired, revoked, or too narrow. | 001 `principals.ts` |
| `filterAccessible(principal, records)` | Removes inaccessible records before ranking or worker-context construction; restricted titles and paths do not survive filtering. | 001 `principals.ts`, called by 019/020/011 |
| `executeStageV1(request, services)` | Admits one pinned argument-vector computation/check invocation, executes through 001, validates declared output/binding and records execution before publication. `StageRequestV1` carries stage ID, principal, execution/attempt IDs, immutable Plan/input/program/environment refs, executable/argv, timeout and declared output schema/path/digests. `StageResultV1` carries execution ref, structured output refs and publication outcome; negative numeric/check outcomes are retained without retries. | 009 `stages.ts`; fixed demo 002 and inquiry controller 003 call it |
| `commitEvidence(request)` | Validates staging and exact evidence binding, then applies commit ID and expected-revision CAS. | 009 `publication.ts` |
| `recoverEvidence(runId)` | Reconciles immutable commit history against the committed run manifest and returns an idempotent recovery report. | 009 `publication.ts` |
| `commitProjectMemory(request)` | Validates records, committed cross-run references and access; applies project-scoped commit-ID idempotency and expected-revision CAS. | 009 `publication.ts`; 019/020/023/014/015/021/022 call it |
| `recoverProjectMemory(projectId)` | Reconciles committed project history and incomplete publication; returns the shared recovery report without promoting orphans. | 009 `publication.ts`; 019 rebuild consumes the recovered committed head |
| `admitProposal(inquiryId, proposal)` | Returns `admitted`, `rejected`, `unresolved`, or `budget_exhausted` with reasons, reservations, identity/match refs, and expected graph revision. Semantic reason remains attributed. | 012 `inquiry.ts` / `budget.ts`; integrated caller 003 `controller.ts` |
| `finalizeInquiry(inquiryId)` | Commits fallback or worker answer card, ledger and terminal outcome after checking required evidence and canonical precedence; completion follows durable memory write. | 003 `controller.ts`, with 020/023 consumers |
| `rebuildIndex(projectId)` | Rebuilds SQLite FTS5 idempotently from committed run/project manifests, import records, ledger rationale and curation events. Preserves stable IDs, scopes, availability, pins, and links. | 019 `catalog.ts` |
| `searchAccessible(principal, query, filters)` | Applies current grant/scope filters before lexical search/ranking; returns accessible IDs, explicit match reasons, and `no_match` or `direct_answer_found`. | 019 index adapter + 020 `retrieval.ts` |
| `serveArtifact(principal, artifactId)` | Resolves committed artifact ID, checks current grant, canonicalizes beneath approved roots, rejects traversal/symlink escape, and returns only declared bytes or access-safe metadata. | 011 `http.ts` |
| `computeAnalytics(request, services)` | Writes a versioned request, invokes the mode-specific Pydantic calculator through the neutral execution package, reads `AnalyticsResultV1`, and verifies exact echoed binding before returning. It never computes statistical values in TypeScript. | Mode facade: 007 `descriptive.ts`; 013 `predictive.ts`; 006 `inferential.ts` |

Controllers serialize stage admission between neutral runner invocations. Worker proposals do not mutate a locked run file. Analytics finalization decides the inquiry outcome from the six canonical values and evidence, never from task status. A valid completed uncertain or negative answer remains a legitimate user outcome.

The first fixed-evidence slice does not depend on the later inquiry controller. Node 009 owns the production single-stage coordinator in `stages.ts`; node 002 invokes it with a frozen synthetic Plan and its own `python/analytics_worker/demo_evidence.py` calculation fixture. Node 003 composes these same stage calls with live worker orientation, proposal admission and inquiry finalization. The fixed fixture is not the descriptive product calculator, which remains owned by 007. Thus 002 has a real execution/publication/check/viewer path before 003 exists, with no dependency cycle or duplicate process runner.

## Canonical operation outcomes

| Boundary | Outcomes |
| --- | --- |
| Commit | `committed`, `replayed`, `revision_conflict`, `rejected` |
| Proposal admission | `admitted`, `rejected`, `unresolved`, `budget_exhausted` |
| Required check | `pass`, `fail`, `inconclusive`, `not_applicable`, `error`; coverage may separately be `missing` or `stale` |
| Answer card retrieval | Per-card `direct_answer`, `related`, `not_relevant`; query `direct_answer_found` or `no_match` |
| Ledger match | `exact`, `related`, `incompatible`, `unresolved` |
| Inquiry finalization | `answered`, `answered_with_uncertainty`, `insufficient_evidence`, `needs_input`, `budget_exhausted`, `execution_blocked` |

No operation maps a negative scientific result to a process error or retry request. A failed integrity condition blocks dependent supported presentation but is retained and can still be reported honestly.

## CLI and viewer paths

### Owned CLI and controller extension seam

Node 001 owns the top-level `src/commands/index.ts` registry, removes the factory route, and registers a computed lazy `analytics` trampoline so its unit builds before 003 exists. Node 003 owns `src/analytics/cli.ts` and exports `analyticsCmd(argv, cmd)` for that trampoline; it also owns `src/analytics/extensions.ts`, the typed analytics extension registry/loader, controller lifecycle, and analytics subcommand dispatch. The 003 production CLI acceptance must invoke the real top-level route. A feature implements a provider in its own facade; it does not edit another node's CLI/controller file. Each provider exports `analyticsExtensionV1: AnalyticsExtensionV1` from the graph-assigned module below.

`AnalyticsExtensionV1` declares `schemaVersion: 1`, unique `extensionId`, `dependsOn: string[]`, optional `commands: AnalyticsCommandV1[]`, and optional hooks. A command declares exact token path, argument schema, required grant operation, and `execute(context, parsedArgs): Promise<CommandResultV1>`. `CommandResultV1` carries exit code, access-safe display payload and committed record refs. Invocation context carries authenticated principal, project/run/inquiry IDs and services for admission, exposure, committed reads, run/project publication and remaining budget; no worker-proposed privilege is trusted.

Hooks are `orient(context)`, `onAdmitted(context, attemptRef)`, `afterExecution(context, executionRef)`, `afterEvidenceCommit(context, manifestRef)`, and `beforeFinalize(context, terminalIntentRef)`. Orientation returns bounded permitted context additions and retrieval/exposure refs. Finalization returns committed project refs. Hooks run in dependency order, with 023 ledger assessment before 020 answer ranking and no duplicate ledger match. Required hook errors halt admission/finalization and preserve history; they are not silently ignored or labeled completed. On retry/recovery, stable event/commit IDs make hook writes idempotent. Controller policy validates hook returns; a hook cannot promote unsupported evidence or change grants/budget/policy.

| Provider module and owner | Extension consumer |
| --- | --- |
| 019 `catalog.ts` (`catalog`) | Catalog/import/export command handlers, committed artifact registration and index projection. Import/retention handlers stay in 019 files. |
| 023 `ledger.ts` (`ledger`, depends on `catalog`) | `ledger show`, orientation comparison, admitted-attempt/execution events, fallback ledger finalization. |
| 020 `retrieval.ts` (`answers`, depends on `catalog`, `ledger`) | `answers search`, prior answer orientation and exposure; `answers.ts` supplies durable answer/fallback creation before completion. |
| 014 `source-discovery.ts` (`source-discovery`, depends on `catalog`) | `source discover`, permitted profiles and candidate references. |
| 015 `skill-refresh.ts` (`source-reuse`, depends on `source-discovery`) | Compatible source-skill orientation and exact recipe-use events. |
| 016 `recurrence.ts` (`recurrence`, depends on `source-reuse`) | Window orientation, correction/invalidation hooks. |
| 017 `authorization.ts` (`use-authorization`, depends on `recurrence`) | Consequential-use request/assessment commands and finalization restrictions for requested use. |
| 021 `skill-capture.ts` (`skill-capture`, depends on `answers`, `source-reuse`) | Bounded feedback/checkpoint capture without activation. |
| 022 `skill-activation.ts` (`skill-activation`, depends on `skill-capture`) | Candidate review/activation commands and scoped later guidance orientation. |
| 006 `inferential.ts` (`inferential-compute`) | Registers the inferential mode calculator with the 003 dispatch seam. Descriptive/predictive providers are already present at 003. |

The loader has a fixed allowlist of these module/export names. It loads available compiled modules at startup and records the capability manifest/digests. During incremental slices, a missing optional provider yields an explicit unavailable capability for its commands; only absence of the exact requested module can be skipped, never its import/runtime errors. A present provider with absent dependency, duplicate command path, invalid schema or cycle fails startup. Required providers are declared by the slice/release acceptance profile, and their absence fails its acceptance gate. This permits later nodes to add production consumers within their own files; it does not use filesystem plugins or import paths supplied by data. `answer show` and `trace` exist in 003 using committed inquiry/result records before 020 arrives; 020 enriches answer retrieval/finalization with project cards through the same hooks. The final release requires all listed providers.

Node 003 owns the public `dag analytics` CLI and Node 011 owns the answer/trace HTTP and viewer surface. These user actions and their meanings are frozen:

```powershell
dag analytics ask --project <path> --question <text> --source <fixture> --model gpt-6-luna --reasoning xhigh
dag analytics answer show --inquiry <id> [--open]
dag analytics trace --claim <id>
dag analytics answers search --query <text> --period <range>
dag analytics ledger show --inquiry <id>
dag analytics source discover --source <fixture>
dag analytics ask --project <path> --question <second-question> --source <fixture> --model gpt-6-luna --reasoning xhigh
```

`ask` resolves the current principal/grant, consults accessible answer cards and the 023 ledger before execution, reserves the bounded budget, and returns an inquiry ID. `answer show` opens the answer card and can open its browser route. `trace` follows the exact assertion chain. `answers search` reports no-match directly and never ranks inaccessible cards. `ledger show` exposes negative, failed, null, incomplete, and deliberate rerun events. `source discover` creates only a scoped candidate; the later `ask` records the exact skill version, supplied references, and actually executed recipe.

The viewer uses stable routes for inquiry/card, claim trace, and committed artifact IDs. It checks principal/grant for each request; browser access is not inherited from the worker's earlier access. It displays `workflow-level` boundaries when the local worker shell can bypass product surfaces.

## Ownership rules for shared files

| Shared file or contract | Sole owner | Consumers/constraint |
| --- | --- | --- |
| `docs/build/ARCHITECTURE.md`, `interfaces.md`, `acceptance-plan.md`, `00-verification.md` | Node 00 | The first three are graph outputs; `00-verification.md` records this node's declared checks. |
| `package.json`, `pnpm-lock.yaml`, TypeScript build dependency changes for the neutral adapter/schema validator | Node 001 | Node 001 adds the immutable package pin and conformance adapter; later nodes request dependency changes from 001. |
| `packages/neutral-execution/`, `artifacts/neutral-execution/`, `pnpm-workspace.yaml` | Node 001 | Sole neutral source, tested legacy adapter, local release archive/digest; analytics consumes exact-version workspace dependency. |
| `python/pyproject.toml`, `python/uv.lock`, `contracts.py`, generated schemas and conformance vectors | Node 001 | Node 008 registers plans against these contracts; 013 and 006 implement mode-specific computation without replacing the common schema. |
| `python/analytics_worker/__init__.py`, `__main__.py` | Node 001 | Shared typed transport/dispatcher; 007/013/006 own their respective calculator modules. |
| `src/analytics/extensions.ts`, `controller.ts`, `cli.ts` | Node 003 | Later domain owners export typed command/lifecycle providers from their assigned facades; they do not edit shared dispatch files. |
| Evaluation fixture registry, shared conformance examples, fixture lock, base evaluator API | Node 18-bootstrap | Feature owners add feature-scoped cases; expected keys/thresholds remain under the lock owner. |
| `src/analytics/analytical-identity.ts` | Node 012 | Node 023 imports it; no duplicate exact-identity predicate. |
| `src/analytics/skill-compatibility.ts` | Node 015 | Node 014/004/022 consume its one source-skill predicate. |
| `src/analytics/acceptance/<feature>.test.ts` and that feature's `spec.md`, `plan.md`, `tasks.md`, `verification.md` | Corresponding feature node | Integration specs exercise the owner; they do not copy primary rule ownership. |
| `docs/build/COMPLETION.md`, `docs/QUICKSTART.md`, `src/analytics/acceptance/99.test.ts` | Node 99 | Final merged-path handover only. |

The implementation ownership summary is in the [graph](graph.json) and [build plan](00-plan.md); this table does not transfer or enlarge a feature facade. Node 019 owns catalog indexing, node 023 owns attempt history, and node 020 alone owns answer-card ranking. The primary T-case owners are the exact entries in [traceability-audit.md](../specs/traceability-audit.md), reproduced without overlapping ranges in the [acceptance plan](acceptance-plan.md).

## Activation consumer ordering — 2026-09-29

Feature 022 is the sole production activation owner for source candidates as well as captured methods/rules. Source activation consumes 014 candidate refs, 015 compatibility, an independent bounded recipe replay under the intended grant, and recorded policy/owner authority and conflict checks. Unknown/conflicting assertions remain unresolved and cannot be promoted as facts. Its skill-activation provider is built before 004. Feature 021 can capture from committed 003 inquiries and 020 answers without a 004 dependency. Feature 015 uses immutable historical active fixture records for early reuse tests; those fixtures are not evidence that activation was executed. Feature 004 consumes the real 022 provider for its live demonstration. No production file ownership is transferred.

The 014 `orient` hook is the production cold-source discovery entrypoint for an ordinary `ask` when committed project memory has zero currently accessible `active` source-skill records for the exact source identity, environment and tenant, and the caller has the required metadata/aggregate grants and remaining discovery budget. This is a record-presence check, not feature 015's compatibility judgment. It invokes the same bounded discovery implementation as `source discover`, commits a `candidate`, and returns only its access-filtered candidate reference and scope metadata. If active references exist, 014 performs no automatic discovery and returns safe references; the later 015 hook applies the sole compatibility predicate and reports incompatible or unresolved guidance without silently substituting an input. An explicit `source discover` command remains available for refresh. Candidates are never supplied as active guidance. No 003 service or dispatcher extension is needed. The 014 acceptance test covers zero-active cold discovery, active-reference deferral, denial and budget stops through the real hook. The 022 provider subsequently performs independent validation and authority review; 004 exercises this full path on a first inquiry before the second inquiry reuses the active version.

The 011 answer/trace read projection includes access-safe linked provenance records reachable from the committed inquiry and assertion. These are generic typed `RecordRefV1` links, including source version, skill version/status, validation and authority events, supplied-reference and executed-recipe events, and outcome-exposure records when they exist. Missing links are shown as missing/restricted; no raw source text, protected title/path or uncommitted record is exposed. The 011 HTTP handler resolves each link through current grants and renders bounded safe labels and exact ID/revision/digest. Later features append those linked records through their own facades; 004 tests the existing 011 projection with actual 014/015/022 records and does not edit viewer code. The 011 feature acceptance covers a committed synthetic linked provenance fixture before 004 exists.
