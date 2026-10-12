# Implementation Plan: Answer a Planned Inferential Question

**Branch**: `006-inferential-profile` (planning path only)  
**Date**: 2026-09-28  
**Spec**: [spec.md](spec.md)

**Input**: Feature 006 specification and the frozen interfaces in
`docs/build/interfaces.md`.

## Summary

Implement one directed inferential profile for the customer-level renewal-rate
difference `rate(group B) - rate(group A)`. A pre-execution
`InferentialPlanV1` fixes the population, customer observation unit, two
operational group definitions, eligible renewal opportunity and period,
direction and units, practical margin, comparison family, dependence and
missingness handling, interval method/level, and inspection rule. The Python
calculator computes the estimate and declared interval. The TypeScript
facade consumes the 001 common computation contract, binds the result through
the shared adapter, and exports `analyticsExtensionV1` for registration by
003. The existing 007 measurement, 010 required-check, and 016 exposure paths
remain authoritative for their own records and transitions.

Observational differences are reported as associations. An imprecise or null
estimate does not establish equality or practical irrelevance. Invalid
sampling, small groups under the frozen policy, unsupported dependence,
missing or changing membership, nonrepresentative scope, undeclared looks,
and causal questions produce explicit scoped limitations while retaining
their result and exposure evidence.

## Technical Context

**Language/Version**: TypeScript and Python in the versions fixed by the
existing root package and 001-owned uv project.  
**Primary Dependencies**: 001-owned Pydantic contracts, generated JSON
schemas, Python dispatcher and dependency lock; 003-owned typed extension
registry and controller; 007 measurement lineage; 010 frozen check policy;
016 repeated-inspection and exposure contract. Use Python standard-library
arithmetic and deterministic resampling where applicable; do not add or
change shared dependencies.  
**Storage**: Existing immutable run-sidecar records, committed outputs and
evidence references; no new storage subsystem.  
**Testing**: `pnpm test`, focused synthetic cases in
`src/analytics/acceptance/006.test.ts`, `uv run --project python pytest`, and
the existing viewer smoke command. These are planned commands; none is run in
this planning stage.  
**Target Platform**: Local analytics CLI/controller and browser answer path.  
**Project Type**: Analytics product feature with an authoritative Python
calculator and TypeScript controller facade.  
**Performance Goals**: Fit within the controller's existing inquiry and
computation budgets; no new throughput target is introduced.  
**Constraints**: Do not change `dag.run.json`, shared contracts, generated
schemas, extension registry/controller, policy thresholds, feature specs,
quality checklist, evaluator answers, or other feature ownership. Use only
synthetic/local fixtures. No external source data or external messaging.  
**Scale/Scope**: Exactly two predeclared groups, a binary renewal outcome,
one named eligible customer population, a fixed period, and one primary
customer-level rate-difference estimand. Causal, survival, and general
hypothesis-testing profiles remain unsupported.

## Constitution Check

### Before design

- Honest answer: pass. The output preserves supported, inconclusive,
  unsupported, and limitation-bearing results instead of reducing inference
  to a significance flag.
- Evidence and execution are separate: pass. Python computes; shared result
  bindings and 010 checks govern supported presentation.
- History and exposure are append-only: pass. Plan revisions and inspection
  exposure are consumed from 008/016; an outcome-informed revision is never
  restored to confirmatory status.
- Scoped and tested memory: pass. No skills or result-memory policy is added.
- Bounded and accurate guarantees: pass. The feature uses existing 003
  budgets, grants, and local workflow boundary labels.
- End-to-end delivery: pass. The owned acceptance test drives an inquiry
  through calculator dispatch and inspects its browser answer/trace.

No gate violation or unresolved requirement was found.

## Design Decisions

See [research.md](research.md) for resolved choices and alternatives. The
main calculation and interpretation rules are:

1. Orient the signed estimate as `p_B - p_A`, with internal units of a
   proportion and user-facing units of percentage points. Positive values
   mean group B renews at a higher observed rate. Keep this orientation even
   when the plan says a lower rate is desirable.
2. Count distinct eligible customer units in each denominator and eligible
   renewals in each numerator. The period, outcome-maturity rule, customer
   reduction rule, exclusions, and group-category meanings are fixed in the
   Plan. The report shows both denominators and any excluded/missing-category
   count.
3. For independent customer units, use the predeclared Newcombe-Wilson
   difference interval; the locked valid reference case uses two-sided 95%
   coverage. For dependence beyond the customer, accept only a declared
   cluster-aware interval and its sampling-cluster definition. A seeded
   cluster bootstrap is the supported cluster path. If the selected method
   cannot handle the observed structure or 010's frozen minimum-sample
   condition fails, return the scoped limitation; never fall back to a
   row-independent interval.
4. Treat a practical margin as a contextual reference in the rate's units,
   not an automatic null or equivalence test. Equivalence and “no meaningful
   difference” claims remain unsupported by this profile.
5. A named comparison family and multiplicity procedure, along with any
   sequential inspection schedule, must be fixed before relevant outcome
   access. Feature 016 owns exposure and inspection transitions. Feature 006
   consumes its locked inspection rule and per-look method; it does not
   create a parallel repeated-look state machine.
6. Use only owner-supplied operational category definitions. A missing
   category is preserved separately or explicitly excluded with its count.
   No group meaning is inferred from names or other personal text.

## Project Structure

### Documentation (this feature)

```text
specs/006-inferential-profile/
├── plan.md
├── research.md
├── data-model.md
├── contracts/inferential-profile-v1.md
├── quickstart.md
├── tasks.md
├── execution.json
└── verification.md
```

### Feature-owned code outputs

```text
python/analytics_worker/inferential.py
src/analytics/inferential.ts
src/analytics/acceptance/006.test.ts
```

`inferential.py` imports the 001 common Pydantic models and implements only
the numeric inferential profile. `inferential.ts` exports
`computeAnalytics(request, services)` and `analyticsExtensionV1`; it uses
the existing 003 extension and computation dispatch seam. The acceptance
test owns seeded synthetic scenarios and verifies a user question through
the existing controller and browser answer/trace. Shared files remain owned
by 001, 003, 007, 010, and 016.

## Dependency and Integration Boundaries

The graph prerequisites for implementation are 003, 016, 007, and 010.
Feature 001 is consumed through the Python package, Pydantic Plan and
computation types, generated schema, and computation adapter already frozen
by the interface contract. In sequence:

1. 007 supplies immutable source/measurement/denominator lineage and
   customer grain.
2. 008 (present through the 003 inquiry path) registers and locks the
   `InferentialPlanV1` and retains plan/exposure history.
3. 010 supplies the frozen method policy, applicable checks, and supported
   presentation eligibility; it owns minimum-sample thresholds.
4. 016 supplies the repeated-inspection schedule, outcome exposure, and
   interpretation transitions.
5. 003 loads the fixed provider module, routes inferential computation, and
   finalizes the live inquiry. The user inspects its answer through 011's
   existing browser path.

No implementation task changes common schemas, dependency manifests/locks,
the controller/extension registry, policy, or viewer code.

## Acceptance Design

The independent story checks and final evidence map are recorded in
[tasks.md](tasks.md), [quickstart.md](quickstart.md), and
[verification.md](verification.md). Acceptance consumes synthetic fixtures
and reference evaluation through the existing evaluator boundary. It covers
the valid risk difference, null and low-power intervals, dependent units,
small groups, missing/changing categories, nonrepresentative populations,
unsupported dependence, unplanned subgroup selection, repeated fixed and
sequential looks, unsupported methods, sensitive-label derivation, and
causal overreach. The live-path case checks the scoped answer and its bound
evidence in the browser.

### Post-design Constitution Check

All six constitutional principles pass after design. No new state vocabulary,
policy threshold, source-access promise, or historical transition is
introduced. Calculation, checks, claim interpretation, exposure, and inquiry
outcome remain distinct. No complexity exception is required.
