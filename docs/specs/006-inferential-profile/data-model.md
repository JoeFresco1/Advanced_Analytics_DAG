# Data Model: Inferential Renewal-Rate Profile

This feature consumes the common Pydantic wire models owned by 001. It does
not define a second Plan or computation schema. Names below refer to the
frozen types and field groups in `docs/build/interfaces.md`.

## Inferential Plan

`InferentialPlanV1` is the immutable directed registration for this profile.
It composes the common registration and directed-plan fields with the
inferential-specific method profile.

| Field group | Meaning and validation |
|---|---|
| Common registration | Version, Plan identity/revision, Question and author refs, predecessor, exact source bindings, declared outputs, and budget ref. Unknown keys fail at authoritative ingress. |
| Population and period | Owner-defined eligible customer population, observation window and outcome-maturity boundary. The named population must match the source and sampling frame or the Plan states the narrower frame. |
| Customer observation unit | Stable customer identity. Multiple source rows require a predeclared reduction to one eligible customer-level outcome; residual dependence requires a declared independent sampling cluster and suitable interval. |
| Group definitions | Exactly two nonempty owner/data-defined membership rules, fixed before directed outcome access. Definitions include effective period and missingness treatment. No personal-name inference. |
| Renewal measure and denominator | Binary renewal outcome for a mature eligible opportunity; distinct eligible customer units per group are the denominator. Numerators, exclusions, missing membership, and category changes remain reconcilable. |
| Estimand | Signed risk difference `p_B - p_A`, internally a proportion and presented in percentage points. The sign meaning and preferred direction are recorded. |
| Practical context | A predeclared margin in the estimand's units with its intended business interpretation. It contextualizes the interval; it does not turn the calculation into an equivalence test. |
| Sampling and uncertainty | Sampling frame, missingness and dependence assumptions; predeclared customer reduction or cluster-aware procedure; fixed method and confidence level; method-policy ref. Unsupported combinations remain limitations. |
| Comparison and inspection | Named comparison family and primary contrast, any policy-approved multiplicity procedure, fixed-look or 016-declared sequential rule, and stopping rule. Each is set before relevant outcome access. |

The profile value is `two_group_renewal_rate_difference`. Unsupported
methods, including causal, survival, and generic multi-group inference, are
not accepted as this profile.

## Inferential Request and Result

`AnalyticsRequestV1` carries the common binding, `mode: inferential`, the
validated `InferentialRequestV1` payload, and declared outputs. Its binding
includes the exact Plan revision, data/source and transformation refs,
program/environment identity, seed, and execution identifiers. The
dispatcher owns common envelope validation and passes the typed mode payload
to the calculator.

The mode result retains the exact common binding and only the fields
permitted by the frozen `InferentialResultV1` payload. It reports:

- The signed risk-difference estimate and the same fixed contrast orientation.
- Group numerators and distinct-customer denominators, plus eligible,
  excluded, and missing-category counts needed to reconcile the population.
- Interval endpoints, method, confidence level, units, sampling unit, and
  declared dependence handling.
- Assumptions and uncertainty interpretation. The Plan reference binds the
  predeclared practical margin, comparison family, and inspection rule;
  008/016 records remain the authorities for lock and exposure history.

An analytically unsupported request uses the existing result, check, and
Claim path to retain the finding and named limitation when its contract is
valid; it is not a process crash or an automatic retry. No feature-local
wire fields are added. Malformed schemas, missing required bindings, and
non-finite numeric values remain contract/evidence errors under 001.

## Supporting Analytical Records

| Record | Relation to this profile |
|---|---|
| Question | Original wording and intended population/use remain owned by the inquiry controller. |
| Plan revision | Fixes all analysis choices before directed execution; later material edits create a linked revision and retain earlier exposure. |
| Source and transformation manifests | Establish customer grain, category provenance, eligibility, outcome maturity, exclusions, and denominator lineage under 007. |
| Exposure and inspection events | Record relevant data/metric access and every declared or undeclared look under 008/016. |
| Result | Stores computed estimate, interval, denominator and exact execution binding. |
| Check Result | 010-owned applicability and validation evidence bound to exact subjects and policy version. |
| Claim | Interprets the association and allowed scope; it cannot turn an association into a causal intervention effect. |
| Report assertion | Shows estimate, denominator, interval, assumptions, practical context, limits, and links to the exact evidence trace. |

## State Boundaries

Use only the canonical vocabularies. An adequately supported association
claim may be `supported`; a usable but imprecise/null inferential conclusion
is `inconclusive`; a claim with failed/missing method support is
`unsupported`. The controller determines the inquiry outcome under the
canonical precedence. A blocked presentation is a controller action, not a
new Claim state. `planned_confirmatory`, `exploratory`, `outcome_informed`,
and `exposure_unknown` are owned by 008/016. Estimate state, process status,
check outcome, Claim evidence, current validity, and intended-use
authorization stay distinct.
