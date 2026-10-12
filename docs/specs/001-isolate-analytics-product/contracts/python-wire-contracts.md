# Python Plan and Computation Wire Contracts

**Schema authority:** `python/analytics_worker/contracts.py` (Pydantic v2).  
**Generated snapshots:** `src/analytics/schemas/`.  
**Shared vectors:** `python/tests/contract-vectors/`.

All serialized JSON is UTF-8, camelCase, and `schemaVersion: 1`. Models use
strict validation and forbid extra fields. Required fields cannot be omitted;
numeric fields reject NaN and infinity. TypeScript validates the generated
Pydantic schemas, not a second handwritten field definition. Both languages
run the exact same vector corpus.

## PlanRegistrationV1

Use a tagged union with discriminator `profile`:

- `ExploratoryPlanV1` (`profile: exploratory`): common registration fields
  only; no invented directed target or confirmatory label.
- `DescriptivePlanV1` (`profile: descriptive`): common and directed fields,
  plus measurement definitions/statistics, units, denominator, missingness
  and exclusions, grouping, and population/period scope.
- `PredictivePlanV1` (`profile: predictive`): common/directed fields plus
  baseline/candidate procedures, feature definitions, prediction time and
  availability, target horizon/maturity, population/evaluation scope,
  split/tuning separation, locked search candidates, primary metric and
  direction, nonnegative `delta`, interval method/level, resampling unit and
  scheme, seed rule, and repeated-evaluation procedure.
- `InferentialPlanV1` (`profile: inferential`): common/directed fields plus
  `methodProfile: two_group_renewal_rate_difference`, customer observation
  unit, exactly two predeclared groups, renewal measure/denominator, period,
  estimand direction/units, practical margin/context, sampling/missingness/
  dependence assumptions, reduction or cluster-aware method, interval,
  comparison family/multiplicity, and inspection/sequential rule.

Common fields include Plan ID/revision, question and predecessor references,
author reference, purpose, source bindings, output IDs/relative paths/roles/
retention/maximum bytes, and budget reference. A source binding is an exact
version reference or an immutable acquisition recipe; mutable `latest` is
invalid. Directed fields include population and target, comparison, method,
partition/search/stopping rules, required-check policy, intended use,
measurement refs, exposure boundary, and uncertainty. A not-applicable
component requires a policy-owned reason.

## Computation envelopes

`AnalyticsRequestV1` contains request ID, mode (`descriptive`, `predictive`,
or `inferential`), computation binding, typed payload, and bounded output
declarations. `AnalyticsResultV1` returns the same request ID/mode, exact
binding echo, typed result payload, produced artifact identities/digests, and
completion time. The binding includes run, inquiry, attempt, execution, Plan
reference, source/transformation references, program digest, Python version,
lock digest, environment digest, and seed.

Mode-specific models are `DescriptiveRequestV1`/`DescriptiveResultV1`,
`PredictiveRequestV1`/`PredictiveResultV1`, and
`InferentialRequestV1`/`InferentialResultV1`. No mode accepts arbitrary JSON.
Mismatched mode, unknown mode, missing/extra fields, invalid references,
non-finite values, or invalid state/unit fails validation. The dispatcher may
validate a profile before its calculator exists; it returns an explicit
unsupported-mode error rather than a dummy result.

## Vector minimum

The corpus includes a valid instance and invalid variants for each of the four
Plan profiles and three computation modes, as well as unknown discriminator,
unknown key, missing required field, wrong-mode payload, mutable source
reference, out-of-range confidence/interval level, negative delta, non-finite
numeric, and unsupported calculator mode. Python and TypeScript report the
same accept/reject decision for each vector.
