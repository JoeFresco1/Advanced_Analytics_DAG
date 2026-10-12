# Independent evaluation registry contract — proposed foundation API

This API is a planning contract for node 18-bootstrap. It reuses the frozen
record reference, access, evaluator-boundary, and append-only-history rules in
`docs/build/interfaces.md`, `docs/specs/access-and-judgment-contract.md`, and
feature 018. It does not implement feature 018's whole-product evaluator or
release assessor.

## Type boundary

```ts
type WorkerFixtureV1 = {
  schemaVersion: 1;
  fixtureId: string;
  profileId: string;
  generatorVersion: string;
  seed: string;
  caseKey: string;
  question: string;
  sourceRefs: string[];
  sourceVersionRefs: string[];
  grantRefs: string[];
  permittedPolicyRefs: string[];
  allowedTaskContext: unknown;
  contentDigest: `sha256:${string}`;
};

type IndependentExpectedOutcomeV1 = {
  fixtureId: string;
  adjudicatorRef: string;
  rubricRef: string;
  rubricDigest: `sha256:${string}`;
  outcomeDigest: `sha256:${string}`;
};

type FeatureFixtureContributionV1 = {
  schemaVersion: 1;
  contributionId: string;
  featureKey: string;
  ownerRef: string;
  parentLockRef: { revisionId: string; digest: `sha256:${string}` };
  fixtureRefs: Array<{
    fixtureId: string;
    featureOwnedPath: string;
    contentDigest: `sha256:${string}`;
  }>;
  independentExpectedOutcomes: IndependentExpectedOutcomeV1[];
  approval: {
    approvalRef: string;
    approverRef: string;
    approvedAt: string;
  };
  targetBatchId: string;
};

type FixtureLockRevisionV1 = {
  schemaVersion: 1;
  revisionId: string;
  revision: number;
  parentLockRef: { revisionId: string; digest: `sha256:${string}` } | null;
  fixtureSetDigest: `sha256:${string}`;
  metricRegistryDigest: `sha256:${string}`;
  contributionRefs: string[];
  fixtureRefs: string[];
};

type EvaluationBatchPinV1 = {
  schemaVersion: 1;
  batchId: string;
  fixtureLockRef: { revisionId: string; digest: `sha256:${string}` };
  metricRegistryDigest: `sha256:${string}`;
  evaluatorVersion: string;
  startedAt: string;
};
```

The evaluator-only `EvaluatorReferenceV1` is not a field of `WorkerFixtureV1`
or the feature contribution's worker-fixture bytes. `caseRole`, tuning
exposure, expected outcomes, relevance labels, and planted-fault definitions
are available only through the evaluator reference loader.

## Operations and failure behavior

```ts
registerFeatureFixtureContributionV1(
  request: FeatureFixtureContributionV1,
  fixtureBytes: ReadonlyMap<string, Uint8Array>
): FixtureLockRevisionV1;

pinEvaluationBatchV1(
  batchId: string,
  fixtureLockRef: FixtureLockRevisionV1['revisionId'],
  metricRegistryRef: string
): EvaluationBatchPinV1;

selectFrozenWorkerFixturesV1(
  batch: EvaluationBatchPinV1,
  fixtureIds: readonly string[]
): WorkerFixtureV1[];

resolveEvaluatorReferencesAfterResponseV1(
  batch: EvaluationBatchPinV1,
  completedResponse: CompletedWorkerResponseV1,
  fixtureIds: readonly string[]
): EvaluatorReferenceV1[];

validateWorkerContextBoundaryV1(
  serializedWorkerContext: Uint8Array,
  contextDigest: `sha256:${string}`,
  evaluatorCanaries: readonly string[]
): void;

adjudicateResponseV1(
  batch: EvaluationBatchPinV1,
  fixtureId: string,
  completedResponse: CompletedWorkerResponseV1,
  contextDigest: `sha256:${string}`
): AdjudicationV1;
```

- The registry instance receives trusted contributor-owner and adjudicator
  authority configuration and a registry clock at construction. Worker or
  feature request data cannot provide or override those authorities or the
  registry admission time.
- `registerFeatureFixtureContributionV1` rejects an unknown feature, mismatched
  `ownerRef`, invalid approval, digest mismatch, ID collision with changed
  content/reference/rubric, absent independent expected outcome, stale parent
  revision, or target batch that has started. `approvedAt` and `registryTime`
  must both be strictly earlier than the target batch start. `registryTime` is
  captured internally at admission, never supplied by the contributor. The
  `approvalRef` must resolve to an immutable approval record from the
  configured owner approver for that feature, bound to the canonical
  contribution/expected-outcome digest, with a timestamp equal to
  `approvedAt`. The independent outcome reference must
  resolve to a configured evaluator adjudicator, not to the evaluated worker
  response.
- An accepted contribution returns a deterministic new child revision. The
  parent lock, its existing expected-outcome references, and the metric
  registry/threshold digest are byte-for-byte unchanged.
- An exact replay returns the same contribution/child revision only when all
  owner, parent, content, expected-outcome, rubric, approval, and target-batch
  bindings match. Changed bytes under an existing ID are rejected.
- `pinEvaluationBatchV1` captures one immutable lock and metric registry after
  checking that every selected contribution's approval and admission precede
  the actual batch start. The registry captures `startedAt` using its trusted
  clock; callers cannot submit or backdate it. `selectFrozenWorkerFixturesV1`
  resolves only that revision even if newer revisions are available. The
  evaluator-reference resolver requires a completed worker response and is a
  distinct API/call boundary; the worker call receives no evaluator array.
- `adjudicateResponseV1` is callable only after a completed response exists.
  The controller calls `validateWorkerContextBoundaryV1` on the serialized
  001 worker packet before worker invocation. It rejects any answer key,
  relevance label, reference ID/digest, or fault-description canary, including
  synthetic retrieved-card, source-skill, and cached-summary additions. The
  expected context digest is SHA-256 of the sanitized packet bytes only.
- An evaluator returning a semantic judgment records its independent
  adjudicator reference and rationale. Mechanical checks do not claim to
  prove arbitrary semantic correctness.
- Evaluation metric registration accepts the exact ten feature 018 definitions
  and requires the configured feature 010 project-owner approval reference and
  a timestamp strictly before batch start. Fixture-contribution ownership and
  approval remain separate from metric-registry approval.
- `FeatureExecutionEvidenceV1` append and disagreement-audit append cannot
  modify fixture locks, expected outcomes, metric thresholds, or prior
  evidence.

## Context canary cases at the 001-only stage

The test invokes node 001's worker-context serialization/digest path. It feeds
synthetic additions shaped as (1) a retrieved answer card from 020, (2) an
active source-skill entry from 014/015, and (3) a cached summary that may
contain earlier evaluation results. It verifies that the actual worker packet
contains none of the answer-key, relevance-label, fault-description,
reference-ID, or reference-digest canaries. A packet with a canary is rejected
before worker invocation. These are contract-shaped synthetic values; they do
not require the later features or claim that the worker shell cannot read the
checkout.

## Ownership of contribution files

Each contributing feature owns its exact descriptor and fixture files, for
example `src/analytics/evaluation/fixtures/contributions/020/retrieval.json`.
The 18-bootstrap execution manifest owns only its exact base-package files,
`registry.ts`, the shared acceptance suite, and the initial lock report. It
does not list a broad fixtures directory or another feature's contribution
path. Registry validation is shared; case content and primary T-case ownership
remain with each feature owner.
