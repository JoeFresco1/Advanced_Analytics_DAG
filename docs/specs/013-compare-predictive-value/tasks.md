# Tasks: Compare Predictive Value

**Input**: Design documents in specs/013-compare-predictive-value/

**Prerequisites**: plan.md, spec.md, research.md, data-model.md, contracts/, quickstart.md

**Organization**: Tasks follow the two P1 user stories. The shared Python
package, Pydantic contracts, source lineage, locked Plan/exposure, required
check policy, and fixture registry are supplied by graph dependencies.

## Phase 1: Setup

The feature adds no shared project configuration or dependencies. Feature
001 owns the Python package/lock and shared schemas; do not edit them here.

## Phase 2: Foundational

There are no additional feature-local prerequisites. Dependencies 007, 008,
010 and 18-bootstrap provide the source/Plan/check/fixture interfaces this
feature consumes.

## Phase 3: User Story 1 - Compare usage against a baseline (Priority: P1)

**Goal**: Return a measured favorable-direction gain and locked uncertainty
for comparable baseline and candidate procedures, then apply the decision
rule only after evidence and required-check eligibility pass.

**Independent Test**: Run the feature acceptance path for known positive,
tightly below-margin and low-power null synthetic comparisons under both
predeclared binary log-loss (lower-is-better) and ROC AUC (higher-is-better)
Plans. Confirm intervals and classifications are computed from the fitted
procedures and locked Plan, with exact evidence bindings and no
favorable-result retry.

### Implementation

- [ ] T001 [US1] Implement the real baseline/candidate pipeline in python/analytics_worker/predictive.py using the existing Pydantic request/result models, comparable evaluation rows, train-only preprocessing, temporal/group partitions, cohort label maturity, declared search/tuning, and the locked interval/resampling parameters; return computed metric, favorable-gain, denominator, population, uncertainty, and evidence-bound result values without owning Claim classification.
- [ ] T002 [US1] Implement the predictive TypeScript facade in src/analytics/predictive.ts using the shared computation adapter to verify exact Plan/source/transformation/code/Python/lock/environment/seed/result bindings, consume current 010 eligibility, reject changed metric/population/denominator/split, and apply the three-way locked interval-versus-delta rule to an eligible Claim only.
- [ ] T003 [US1] Add end-to-end acceptance scenarios for positive, tight below-margin and low-power null comparisons in both predeclared log-loss and ROC AUC fixtures in src/analytics/acceptance/013.test.ts; assert computed outputs against frozen evaluator tolerances, TypeScript classification after passing checks, exact binding, the predictive target and evaluation population in the structured result without a causal assertion, and no retry for a valid null.

**Checkpoint**: A valid baseline/candidate comparison yields improvement,
meaningful_improvement_excluded or inconclusive from computed values and the
locked favorable-gain interval.

## Phase 4: User Story 2 - Catch invalid evaluation (Priority: P1)

**Goal**: Block supported predictive Claims when temporal, maturity,
dependence, preprocessing, search or comparability conditions invalidate the
evaluation, while retaining the exact failed evidence and local access limit.

**Independent Test**: Run the same Python producer and TypeScript facade
against injected invalid variants. Each failed required condition must
produce an unsupported Claim with the specific condition visible and no
Claim-level predictive classification. A locally readable withheld
partition must be labeled workflow-withheld.

### Acceptance scenarios

- [ ] T004 [US2] Extend the feature-owned acceptance file src/analytics/acceptance/013.test.ts with post-cutoff feature, cohort-specific immature label, repeated-entity split, temporal leakage, globally fitted preprocessing, evaluation-set tuning, unsupported dependence, post-result search expansion, changed population/metric/denominator, and workflow-withheld cases; assert their specified failed-check, exposure, unsupported-Claim or access-label states without changing shared fixtures or retrying.

**Checkpoint**: Invalid comparisons remain inspectable as failed required
conditions and unsupported Claims; valid uncertainty remains distinct from
invalid evidence.

## Phase 5: Polish and cross-cutting verification

- [ ] T005 Record actual commands, fixture outcomes, evidence bindings, limitations and FR/SC evidence mappings in specs/013-compare-predictive-value/verification.md after running the Python package check and complete pnpm test acceptance suite.

## Dependencies and execution order

- User Story 1 is the MVP and consumes graph dependencies 007, 008, 010 and
  18-bootstrap. Within this feature, T001 precedes T002, which precedes T003.
- User Story 2 builds on the producer and facade from User Story 1. T004
  extends the same acceptance file after T003, so the two tasks are serial.
- T005 is the final feature unit. It records evidence only after T004 and
  runs the complete acceptance suite.

## Parallel opportunities

No feature tasks are parallelizable: the calculator, facade and acceptance
scenarios build on one another, and T003/T004 both edit the single
graph-owned acceptance file.

## Implementation strategy

Deliver User Story 1 first: compute the comparison in Python, bind and
classify it in TypeScript after checks, then prove the valid decision states
end to end. Add invalid-evaluation fixtures in User Story 2 and finish by
running the full acceptance suite and recording requirement-level evidence.
The final verification must reject skipped/TODO cases and retain failures as
evidence.

## Coordinator decomposition amendment (2026-10-04)

The execution manifest defines the new bounded order and acceptance commands. Test-first setup stays with its production dependency. Each intermediate unit adds executable `scope:<unit-key>:` behavioral tests with positive and failure controls; final feature acceptance remains complete. Original obligations split across new IDs are retained in `docs/build/decomposition-audit.json`; coverage maps reference their full replacement task set. Existing requirement, threshold, interface and evidence rules remain normative.
