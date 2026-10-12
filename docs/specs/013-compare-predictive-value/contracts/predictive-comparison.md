# Predictive Comparison Contract

This feature consumes the shared version-1 AnalyticsRequestV1,
AnalyticsResultV1, PredictiveRequestV1 and PredictiveResultV1 models. Their
normative definitions are the strict Pydantic contracts owned by 001 and
their generated JSON schemas. This document defines feature behavior and
does not create a second JSON schema or duplicate common envelope.

## Python producer

The mode calculator exports the shared Python dispatcher entrypoint
compute(request). It must:

1. Validate and use only the registered locked baseline, candidate,
   population, split, feature-availability, target-maturity, metric,
   direction, search, tuning and uncertainty parameters.
2. Construct comparable pipelines. Fit every learned transformation within
   each training partition/fold; apply the fitted transformation to its
   held-out rows.
3. Keep temporal training observations before evaluation observations and
   group partitions entity-disjoint as required by the locked design.
4. Evaluate baseline and candidate on identical eligible rows and report the
   denominator and evaluation population.
5. Convert metric differences to favorable direction, and estimate the
   interval with the declared method, level, resampling unit, scheme and
   seed. Do not replace an unsupported method/unit combination with a
   convenient alternative.
6. Return the strict shared PredictiveResultV1 and declared output
   references. Do not invent expected metric values or fixture-specific
   result branches.

## TypeScript consumer

The feature-owned predictive facade implements the frozen
computeAnalytics(request, services) behavior. It writes and dispatches the
exact request through shared services; it does not calculate a second
statistical model. Before applying any predictive class, it:

- validates the Pydantic-generated mode schema and exact echoed binding;
- checks the Result and declared output digests against the registered
  Plan/input/program/environment refs;
- obtains the frozen 010 required-check coverage and
  supported_presentation_eligible decision for the exact dependent Claim;
- confirms candidate/baseline metric, denominator, population, split,
  evaluation time and uncertainty protocol match the locked Plan.

Only after these checks pass, apply the locked favorable-gain interval rule:

| Condition | Predictive comparison |
| --- | --- |
| interval lower bound > locked delta | improvement |
| interval upper bound <= locked delta | meaningful_improvement_excluded |
| interval spans locked delta | inconclusive |

The TypeScript decision is the Claim-level classification. If the strict
Python payload contains a matching computed class, recompute and compare it;
a mismatch is an evidence error. A failed, inconclusive, error, missing or
stale required condition results in unsupported Claim evidence and no
Claim-level predictive classification. A valid low-power null interval that
spans delta is inconclusive and is not retried.

## Stable evidence and user-visible limits

Preserve run, inquiry, attempt, execution, Plan, source, transformation,
program, Python, lock, environment, seed and output references exactly. The
Claim trace follows the Result, required checks, execution and data/code
versions. Report the predictive estimand, metric and units, favorable gain,
interval, delta, denominator, population and limitations. A predictive
comparison does not establish causation.

When the local worker can read a delayed evaluation partition, label it
workflow-withheld; access isolation not enforced. A failed check remains
visible in the trace; no broad “verified” label is introduced.
