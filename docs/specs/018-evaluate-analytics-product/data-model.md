# Data Model: Evaluation and Release Assessment

Feature 018 consumes reference definitions from the bootstrap registry and
records results from completed runtime inquiries. These evaluation records do
not replace or mutate Question, Plan, Execution, Result, Claim, Check Result,
answer-card, artifact, ledger, skill, grant or policy records.

## MetricRegistryEntryV1 (bootstrap-owned, consumed read-only)

- **Identity**: metric ID, registry version, applicable fixture/profile and evaluator version.
- **Policy**: formula, numerator selector, denominator selector, favorable direction, minimum cohort, threshold and units.
- **Authority**: configured project-owner actor reference and approval timestamp, both preceding the governed batch.
- **References**: fixture set, evaluator/rubric and exact content digests.
- **Validation**: every required field is present, profile applies, references resolve, actor is authorized and timestamp precedes batch start. Missing data is invalid rather than defaulted.

## EvaluationBatchV1

- **Identity**: evaluation ID/revision, fixture-registry version, lock digest, evaluator/rubric versions, ordered fixture IDs and batch timestamps.
- **Execution**: each actual inquiry/run reference, worker/harness/model/reasoning version, prompt/context digest, source and grant profile, policy version, environment/resource settings and fixture allowance.
- **Isolation**: allowed-context reference/digest, answer-key/fault-description exclusion audit and declared isolation guarantee (`workflow-withheld; access isolation not enforced` for the local shell unless an enforced boundary is proven).
- **Integrity**: reference digests captured before work and rechecked after scoring; any mismatch or detected exposure invalidates the batch.
- **Observations**: one case record per expected fixture, retaining complete, uncertain, null, negative, conflicting, blocked, incomplete and failed outcomes as observed. No absent case is dropped from the expected denominator.

## CaseObservationV1

- Fixture and actual inquiry/evidence references.
- Scoped worker answer, claim/evidence state, limitations, report assertions, question outcome and required-check coverage.
- Worker settings, source version, grants, allowance, elapsed time, invocation and branch counts, observed cost or explicit `unavailable`.
- Evidence-chain, output inventory/export, notebook replay, unmanaged-write, catalog, answer-retrieval, ledger, skill proposal/validation/reuse and access observations where applicable.
- Reference-only adjudication, fault labels and rubric result, populated only by the separate evaluator after the worker response.
- Unavailable or not-applicable observations carry a reason and do not silently become a zero or pass.

## MetricObservationV1

- Frozen metric ID and registry reference.
- Exact numerator, denominator, derived value or unavailable state, units, favorable direction, minimum cohort and threshold.
- Case IDs and committed evidence references used in each count; any exclusions must be named and policy-authorized.
- Comparison group/profile and confidence/uncertainty output where the metric definition requires it.

Rates are computed only from the frozen formula. The report renders integer counts and denominator beside every rate. A zero or undersized denominator is a gate failure even when the quotient would otherwise look favorable.

## AuditSampleV1

- Sampling frame and registry/policy version; risk strata and frozen selection rule/seed; selected disagreement records; adjudicator actor; disagreement severity.
- Numerator, denominator, target precision and uncertainty interval.
- `descriptiveOnly` is true when sample size misses the predeclared precision target. Descriptive-only samples cannot alter policy thresholds or support a broad calibration claim.

## ReleaseAssessmentV1

- Frozen batch, registry, evaluator and metric-observation references.
- Profile key, batch integrity/isolation result, metric-by-metric counts and threshold decisions, missing-observation and denominator failures, and linked evidence.
- Separate `fixtureProfileEligible` and `generalReliabilityEligible` results. Fixture-profile eligibility requires all profile gates to pass. General reliability additionally requires its own predeclared population, sample-size rationale, uncertainty interval and separate held-out batch.
- Exact failed metric, numerator, denominator, threshold, minimum, and evidence references. Eligibility never changes any evaluated claim or inquiry state.

## Relationships and state boundaries

An evaluation batch contains case observations; case observations produce metric
observations; a release assessment consumes the batch and metrics. Reference
material has evaluator-only access. A batch is invalidated on reference or
context-integrity violation. Metrics may be unavailable; release policy decides
which observations are required and fails closed when a required value cannot
be observed. An assessment is not an analytical Claim, authorization decision,
or worker inquiry outcome.
