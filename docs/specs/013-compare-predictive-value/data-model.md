# Data Model: Compare Predictive Value

This feature consumes the version-1 objects and references owned by earlier
features. The entries below describe their predictive role; they do not
introduce a parallel record or wire model.

## Existing entities

### Registered predictive Plan

**Owner**: 008.

**Identity**: Exact Plan record reference with revision and digest.

**Relevant content**: Baseline and candidate procedures; source/feature
definitions; prediction time; feature availability; target horizon and label
maturity; population and evaluation scope; temporal/group partition design;
tuning separation; locked candidate search; primary metric and units;
favorable direction; nonnegative delta; interval method and level; resampling
unit and scheme; seed and repeated-evaluation procedure; required-check
policy; exposure references and plan_interpretation.

**Validation**: Registration is validated against the shared
PredictivePlanV1. A substantive change is a new linked Plan revision. This
feature never rewrites the Plan or its exposure.

### Computation binding and request

**Owner**: 001 shared contract; request production uses the existing
computation adapter.

**Identity**: AnalyticsRequestV1 request ID plus its ComputationBindingV1.

**Relevant content**: Run, inquiry, attempt and execution IDs; exact Plan
reference; source and transformation references; program digest; Python
version; dependency-lock digest; environment digest; and declared seed.
PredictiveRequestV1 carries the locked comparison inputs defined by the
registered Plan.

**Validation**: Unknown fields, missing dimensions, invalid states,
non-finite values and mode mismatches fail through the strict shared model.

### Evaluation population and partition

**Owner**: 007 source/data identity plus 008 Plan registration; computation
and method-specific checks are consumed by 013.

**Identity**: Exact source/data and transformation references, plus the
locked population and partition roles in the Plan.

**Relevant content**: Observation/entity key, prediction timestamp, feature
availability, outcome horizon, label availability/maturity, partition
membership, denominator and evaluation access profile.

**Validation**: Temporal boundaries and group disjointness are checked
against the registered split. Feature and label times are compared with
prediction/evaluation cutoffs. Unknown required metadata cannot pass.

### Baseline/candidate computation and interval

**Owner**: Python predictive calculator in this feature.

**Identity**: PredictiveResultV1 bound to the request and exact computation
binding; produced output artifacts have stable EvidenceRefs and digests.

**Relevant content**: Both fitted procedures; their evaluation metrics on the
same rows; favorable-direction gain; interval bounds and locked method/level;
denominator; population; seed; and declared output references.

**Validation**: The calculator fits preparation on training rows only and
does not tune on evaluation rows. Numeric fields are finite and conform to
the existing Pydantic result contract. Results are generated from the input
fixture and procedure, not from fixture-specific constants.

### Required Check Result and eligibility

**Owner**: 010.

**Identity**: CheckResultV1 with policy, validator and exact subject
references. Eligibility is calculated for the dependent Claim by the frozen
policy.

**Relevant content**: Temporal availability, entity overlap, label maturity,
preparation/tuning leakage, denominator/population comparability, search
scope and supported interval/dependence conditions as applicable.

**Validation**: Check outcomes remain pass, fail, inconclusive,
not_applicable or error. Missing/stale coverage is separate. A failed,
inconclusive, error or unsatisfied required condition blocks Claim
eligibility; it is not a Python process failure and is not retried to get a
different answer.

### Predictive comparison classification

**Owner**: TypeScript predictive facade in this feature, after 010 eligibility
and exact result-binding checks pass.

**Identity**: Predictive comparison dimension attached to the supported
interpretation of the exact Claim/Result pair.

**Values**: improvement, meaningful_improvement_excluded, inconclusive.

**Rule**: Compare the favorable-direction interval with the Plan's locked
delta: lower bound strictly above delta is improvement; upper bound at most
delta excludes the predeclared meaningful gain; otherwise the interval spans
delta and is inconclusive.

**Validation**: This value is distinct from Claim evidence state, Check
outcome, inquiry outcome and use authorization. A failed required check
produces an unsupported Claim without a classification. A valid imprecise
interval remains a valid inconclusive predictive comparison.

## Relationships

~~~text
RegisteredPredictivePlanV1
  └─ AnalyticsRequestV1 + ComputationBindingV1
       └─ PredictiveResultV1 + ProducedArtifactV1
            ├─ CheckResultV1 / frozen policy eligibility
            └─ TypeScript predictive comparison classification
                 └─ Claim and inspectable Result evidence trace
~~~

Each edge uses stable refs/digests. Paths are storage locations, not evidence
identity. A changed Plan, input, transformation, program, environment,
output, check subject or policy requires a new binding/check assessment.

## State boundaries

- Python process status records whether a command executed; it does not stand
  in for metric direction, interval result or required-check outcome.
- CheckResult outcome records validation. It does not overwrite the
  numerical Result.
- Predictive comparison classification describes the locked interval rule.
  It does not by itself make the Claim supported or authorize a use.
- Claim evidence state and inquiry outcome follow the canonical vocabulary
  and remain separately attributable.
- Workflow withholding records the actual local access boundary; it does not
  assert independent isolation.
