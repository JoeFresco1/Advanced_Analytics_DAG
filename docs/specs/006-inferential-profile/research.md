# Research: Two-Group Renewal-Rate Difference

This is bounded design research against the accepted feature specification
and frozen repository contracts. It introduces no new product requirement.

## Decision: Estimate a signed customer-level risk difference

Use the two predeclared operational groups and report
`renewal_rate(group B) - renewal_rate(group A)`. Compute with proportions on
the `[0, 1]` scale and present the same estimate and interval in percentage
points. Keep the orientation stable; do not reverse it to make positive mean
“better.”

**Rationale:** The feature asks for a two-group renewal-rate difference and
SC-006 names the reference risk difference. A signed, unit-labeled contrast
is directly interpretable and avoids a significance-only output.

**Alternatives considered:** A ratio or odds ratio changes the requested
estimand; a p-value-only test loses effect magnitude and interval context;
choosing whichever measure gives the strongest result violates the locked
comparison contract.

## Decision: Use Newcombe-Wilson for independent customer units

For two independent customer-level binary rates, use the Newcombe hybrid
score interval formed from each group’s Wilson score limits. The fixed valid
reference fixture uses a two-sided 95% interval. The declared Plan fixes the
method and confidence level before directed execution. A small group that
violates 010’s frozen method-policy applicability rule produces a named
limitation and is not repaired by substituting an easier interval.

**Rationale:** The method aligns with the binary risk-difference estimand,
uses a finite-sample score interval, and can be implemented with Python’s
standard library. The feature’s requirement is an uncertainty interval, not
a test selected after observing the data.

**Alternatives considered:** A Wald interval is poorly behaved at small or
boundary proportions. A generic bootstrap does not by itself validate the
sampling design. A different method is acceptable only when the locked Plan
and 010 policy declare it before outcomes are inspected.

## Decision: Resolve dependence at the declared sampling unit

The observation unit is a customer. Duplicate/longitudinal input rows are
usable only when the Plan predeclares a customer-level reduction that defines
one eligible renewal opportunity and one binary outcome per customer. If
sampling dependence remains across customers, the Plan must name the
independent sampling cluster and select a cluster-aware interval; the
supported path is a seeded cluster bootstrap over those declared clusters.
Use the existing policy’s sample/cluster minimum. If the selected interval
cannot handle the observed structure, preserve the finding and return the
named limitation without calculating a row-independent fallback.

**Rationale:** FR-011 requires a customer unit and allows either a valid
customer-level reduction or justified cluster-aware interval. Explicit
cluster resampling preserves within-cluster dependence and makes the seed,
cluster definition, draws, and interval interpretation auditable.

**Alternatives considered:** Treating every row as independent is invalid
for repeated customers. Averaging an unspecified set of customer records
changes the estimand. A design-effect correction with no validated input
assumptions is not a general repair.

## Decision: Keep the practical margin contextual

Record the predeclared absolute practical margin in the Plan’s rate units,
its direction, and a short practical interpretation. Show the estimate,
interval, margin, and its relation to the margin together. The margin does
not make a non-significant result equivalent to no difference. This profile
does not claim equivalence or practical irrelevance.

**Rationale:** FR-005 and FR-006 require magnitude and practical context while
prohibiting unsupported equality conclusions.

**Alternatives considered:** A post-result margin, a binary “significant /
not significant” classification, or interpreting an interval that overlaps
zero as equality are all disallowed.

## Decision: Reuse 016 and 010 for inspection and comparison governance

The Plan names the comparison family, primary comparison, multiplicity
procedure, fixed or sequential inspection rule, and stop rule before outcome
access. 016 remains the authority for exposure, look recording, and
`plan_interpretation`; 010 remains the authority for check policy and
supported-presentation eligibility. A post-result selected subgroup stays
exploratory/outcome-informed. A declared sequential design consumes its
locked per-look procedure and records every look. No favorable-result retry
is admitted.

**Rationale:** This is the boundary frozen by the state vocabulary,
architecture, and interface documents. It prevents feature 006 from
creating a second exposure or policy state machine.

**Alternatives considered:** Relabeling after owner approval, rerunning until
significance, or implementing feature-local confirmatory flags would
rewrite history or duplicate normative behavior.

## Decision: Keep operational group meaning and representativeness explicit

Use only two owner-defined category rules supplied by the source or Plan.
Retain missing membership as a separate category or a documented exclusion
with its denominator. Compare category-definition versions across periods.
If the sample does not represent the named population, qualify scope to the
observed sample or return a limitation. Never infer gender or another
sensitive membership from names. An observational comparison supports an
association only; causal questions outside this profile return a scoped
unsupported-method result without an inferential estimate.

**Rationale:** These are direct FR-007/008 obligations and feature edge
cases. Source meaning and denominator lineage belong to 007.

**Alternatives considered:** Name heuristics, silent missing-value pooling,
assuming convenience data represent all customers, or describing group
membership as an intervention are not valid alternatives.

## Decision: Use only frozen dependencies and types

Pydantic in 001 remains the normative Plan and mode-payload schema. Python
calculates numerical values; TypeScript verifies/binds through the shared
adapter and registers the mode provider using the 003 seam. Use existing
locked dependencies only; no `pyproject.toml`, `uv.lock`, generated schema,
or shared interface change is part of 006.

**Rationale:** Architecture and graph assign those files to 001/003 and
freeze the `InferentialPlanV1`, `AnalyticsRequestV1`, `AnalyticsResultV1`,
and `AnalyticsExtensionV1` boundaries.

**Alternatives considered:** Handwritten TypeScript statistics, a duplicate
Pydantic contract, changing the shared loader, or pinning a new library from
feature 006 would violate single-owner interfaces.
