# 18-bootstrap data model

This model defines the fixture and evaluator records planned for the
foundation API. It preserves the accepted analytics states and does not add a
new inquiry, check, claim, or release state.

## Records

### `WorkerFixtureV1`

Worker-visible synthetic input for one case.

- `schemaVersion`, `fixtureId`, `profileId`, `generatorVersion`, `seed`, and
  `caseKey` establish reproducible identity.
- `question`, `sourceRefs`, `sourceVersionRefs`, `grantRefs`, permitted policy
  refs, and allowed task context establish the worker's inputs.
- `contentDigest` binds canonical worker-visible bytes.
- It has no answer, relevance label, case role, reference ID/digest, or
  planted-fault description.

### `EvaluatorReferenceV1`

Evaluator-only record resolved after a completed worker response.

- `fixtureId`, `caseRole` (`development` or `evaluation`), and `tuningExposure`
  bind scoring eligibility and exposure history.
- `expectedOutcomeLimits` records accepted scoped outcomes, permitted honest
  uncertainty/non-answer outcomes, forbidden extrapolations, units,
  denominator, numeric comparison rule, and any predeclared tolerance.
- `plantedFaults` lists the fault variant, description, and intended blocking
  condition where applicable.
- `rubricRef`, `rubricDigest`, and `evaluatorVersion` pin adjudication rules.
- `referenceDigest` covers the canonical evaluator-only record.

### `SyntheticSourceProfileV1` and `SyntheticGrantProfileV1`

- A source profile pins synthetic `sourceId`, source version, schema/meaning
  labels, generator rule, and content digest.
- A grant profile identifies the principal or role, source, permitted
  operation, audience, effective period, and owner reference using the
  canonical Principal/SourceGrant field meanings.
- Profiles cover aggregate access, restricted row/sheet access, and denied,
  expired, or revoked access.
- A grant authorizes source access only; it never grants reference-key access.

### `FixtureSetRevisionV1`

Immutable snapshot of fixture and evaluator-reference bindings.

- `revisionId`, monotone revision, parent revision ref (absent for the initial
  revision), canonical content digest, generator versions/seeds, and profile
  IDs define the snapshot.
- `fixtureRefs` bind worker fixture IDs/content digests to evaluator reference
  and rubric digests without adding evaluator values to worker records.
- `roleDigest` and exposure refs freeze development/evaluation assignments.
- The initial revision includes fixed cohorts and denominator declarations.
- A new revision appends feature-scoped cases; it never changes prior refs,
  roles, expected outcomes, rubrics, or metric thresholds.

### `EvaluationMetricV1` and `MetricRegistryRevisionV1`

- Each metric carries `metricId`, formula, numerator definition, denominator
  definition, favorable direction, minimum cohort, threshold literal,
  applicable profile, fixture-set digest, evaluator version, approval actor
  ref, and approval timestamp.
- The registry revision carries its stable ID/digest and exactly the ten
  initial feature 018 gates.
- Owner approval may remain pending before initial product evaluation. An
  actual batch requires configured owner attribution and an approval time
  strictly before that batch starts.
- Cost is observed or unavailable and is not a numeric gate. The five
  no-match false-direct-answer outcome is a separate fixed check.

### `FeatureFixtureContributionV1`

Append-only request to add a feature-owned case cohort.

- `contributionId`, `featureKey`, `ownerRef`, and configured owner authorization
  identify who may contribute.
- `parentLockRef` identifies the exact locked revision to extend.
- `fixtures` bind each new `fixtureId` to its worker fixture content digest and
  feature-owned file reference.
- `independentExpectedOutcomes` bind new IDs to independent adjudicator refs,
  rubric refs, and outcome digests. The worker's response is never their
  authoring input.
- `approval` records the approving actor/reference and timestamp.
- `admittedAt` is produced by the registry; both it and approval must precede
  the target batch start.
- Existing ID with changed bytes or expected outcome is rejected as a
  collision. Exact replay of the same immutable binding may be idempotent.

### `FixtureLockRevisionV1`

Immutable result of an accepted contribution.

- `revisionId`, revision number, `parentLockRef`, contribution ref/digest, and
  new lock digest identify the child.
- It carries inherited fixture/reference/rubric refs, the appended contribution
  refs, and unchanged metric-registry/threshold digests.
- The parent remains readable and unchanged.

### `EvaluationBatchPinV1`

- `batchId`, exact `fixtureLockRef`, exact metric-registry ref, evaluator
  version, worker configuration ref, and `startedAt` identify the batch.
- Once started, its fixture and expected-outcome selection cannot change.
- A contribution to that batch after start is rejected. A later batch may pin
  a newer child revision.

### `AdjudicationV1`, `FeatureExecutionEvidenceV1`, and
`ReviewDisagreementAuditV1`

- Adjudication binds completed response digest, case/reference/rubric/evaluator
  refs, deterministic result, and independent semantic judgment attribution
  and rationale where needed.
- Feature execution evidence records feature owner, case refs, worker/tool/model,
  harness/version, policy/resource settings, source/prompt/context digests,
  fixture/rubric digests, observed result, and artifact/evidence refs.
- A review disagreement audit records sampling frame, policy version, risk
  strata, selection rule, adjudicator, severity, counts, numerator/denominator,
  uncertainty, and precision target. Undersized samples are descriptive only
  and cannot change policy or threshold digests.

## Relationships and invariants

```text
WorkerFixtureV1 ──fixtureId── EvaluatorReferenceV1 ──rubricRef── Rubric
      │                                │
      └──────── content digest ────────┴── FixtureSetRevisionV1
                                               │
FeatureFixtureContributionV1 ──parent─────────┤ creates immutable child
                                               ▼
                                      FixtureLockRevisionV1
                                               │ exact ref
                                               ▼
                                      EvaluationBatchPinV1
                                               │ completed response
                                               ▼
                                         AdjudicationV1
```

- A worker packet contains only worker fixtures and permitted granted inputs.
- The evaluator interface receives references/labels only after response
  completion. Access to source fixtures does not grant reference access.
- `caseRole` and tuning exposure are evaluator-only but included in locked
  fixture-set identity.
- Thresholds and expected outcomes are immutable after a batch starts.
- A changed source/grant, generator, fixture, reference, rubric, or evaluator
  version creates a new immutable revision rather than rewriting history.
- Query/card retrieval labels, the 99-file inventory expectations, and
  capture-signal labels remain independent of product output.
