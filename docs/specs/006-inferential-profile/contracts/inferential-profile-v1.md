# Inferential Profile Contract (Feature 006)

## Authority and transport

The authoritative schemas are the 001-owned `InferentialPlanV1`,
`AnalyticsRequestV1`, `InferentialRequestV1`, `AnalyticsResultV1`, and
`InferentialResultV1`. Pydantic is normative; TypeScript consumes generated
schemas and common conformance vectors. This file records feature semantics
and does not replace or extend those wire schemas. A needed shared schema
change requires its owner and a versioned migration; feature 006 does not
edit common contracts.

The mode is `inferential`; the method profile is
`two_group_renewal_rate_difference`. Common request/result envelopes echo an
exact execution binding and declared artifact references. Python is the
numeric calculator. The TypeScript `computeAnalytics(request, services)`
facade uses the shared adapter and validates the echoed binding; it does not
calculate statistical values.

## Locked analysis semantics

- Contrast: `p_B - p_A`; positive means the observed renewal proportion is
  higher for group B. Internal unit is a proportion; displayed unit is
  percentage points.
- Outcome: binary renewal by the Plan's maturity cutoff for an eligible
  renewal opportunity within its fixed period.
- Observation/denominator: distinct eligible customers under 007's exact
  grain and source version. The output reconciles events/rows to customers,
  each group's numerator and denominator, missing membership, and exclusions.
- Groups: exactly two supplied operational category definitions, fixed
  before directed outcome access. Missing membership is kept separate or
  excluded explicitly with its count. Definitions are versioned across
  periods. Names cannot supply gender or any sensitive category.
- Independent-sample interval: predeclared Newcombe hybrid Wilson score
  interval. The valid reference fixture uses a two-sided 95% interval.
- Dependence: repeated rows must be reduced under a predeclared valid
  customer-level rule. Remaining cluster dependence requires a named
  independent cluster and a predeclared cluster-aware interval; the supported
  cluster procedure resamples those clusters with the locked seed and
  procedure. If the observed design or 010 policy minimum is not supported,
  report a scoped limitation; do not substitute an independent-row interval.
- Comparison: one named primary contrast belongs to a declared family. Any
  multiplicity procedure is fixed under the applicable method policy before
  outcome access. Outcome-selected subgroups remain exploratory.
- Repeated looks: use the frozen 016 inspection rule. The Plan records the
  full schedule, error/sequential procedure, and stop rule before the first
  relevant look. Every look is recorded. An undeclared look records exposure
  and blocks the original fixed-look confirmatory interpretation.
- Practical context: the margin, direction and business meaning are fixed
  before execution. The profile does not claim equivalence or “no meaningful
  difference” from a null or imprecise result.
- Claim limit: the comparison is observational association. It cannot say
  that changing group membership would cause renewal to change. A causal
  question is an unsupported-method limitation and produces no inferential
  estimate.

## Outcomes and error boundary

Well-formed but unsupported assumptions retain the estimate/result where
valid and name the failed assumption through the existing result, required
check, and Claim/report path. No new payload field or state is introduced.
010's exact check outcomes and presentation eligibility govern whether a
dependent claim may be shown as supported. Null/low-power estimates remain
available with their interval and do not prove equality. A malformed
envelope, stale/mismatched evidence binding, or invalid payload remains an
evidence/contract error. Neither an analytical limitation nor an unfavorable
result requests a favorable retry.

## TypeScript provider boundary

`src/analytics/inferential.ts` exports `analyticsExtensionV1` with the frozen
extension ID `inferential-compute`, imports/registers through the 003 fixed
provider allowlist and dispatch seam, and routes the inferential mode to the
Python calculator via the shared computation adapter. It must not edit
`src/analytics/extensions.ts`, `controller.ts`, `cli.ts`, or the 001 common
Pydantic model, generated schema, or package lock.

## Verification boundary

Feature acceptance consumes synthetic/local reference and fault fixtures
through the existing 18-bootstrap evaluator boundary. Reference answers and
fault explanations are unavailable to the live worker context. The acceptance
path begins with a user question and ends with a browser answer and evidence
trace; a calculator-only pass is insufficient.
