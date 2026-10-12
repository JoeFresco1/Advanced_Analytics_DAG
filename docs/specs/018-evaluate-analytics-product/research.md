# Research: Whole-Product Evaluation

## Decision 1: Use the bootstrap fixture registry as the only reference authority

**Decision**: Consume the immutable fixture, generator, grant, expected-answer, fault, rubric, evaluator and metric references exposed by `18-bootstrap`. Feature 018 adds no fixture cases to the shared registry and never edits its lock.

**Rationale**: The graph assigns the registry, fixture package, initial metric thresholds and reference evaluator API to `18-bootstrap`. Feature 018 owns the whole-product scorer and release assessment. Separate ownership prevents the implementation under evaluation from editing the answer key or policy it is judged against.

**Alternatives considered**: Duplicating expected answers in `018.test.ts` would create a second mutable answer key and is rejected. Hard-coding fixture answers in the runtime scorer would replace runtime evidence with fixture-only answers and is rejected.

## Decision 2: Run the real worker first, then release references to an independent evaluator

**Decision**: The batch runner invokes the existing question-to-answer worker/controller path with the requested synthetic case and allowed context. Only after the worker returns does an evaluator-only reader load reference answers, fault definitions and adjudication labels. Pin all reference versions and verify their digests before and after scoring.

**Rationale**: This follows proposal §20, the architecture's evaluation boundary, FR-002/014 and SC-002/009/015. It permits scoring actual answer and evidence behavior while preventing the scorer from supplying the answer to the worker.

**Alternatives considered**: Feeding the answer key into a test adapter, generating an answer from fixture data, or directly returning a canned fixture result would fail the required live-worker path and is rejected. Strong filesystem holdout cannot be claimed where the local harness can read fixture files; use the architecture's explicit workflow-withheld label.

## Decision 3: Treat the accepted metric registry as a pre-batch policy object

**Decision**: Validate registry completeness, profile applicability, the configured project owner's approval under feature 010 FR-008, and approval time before batch start. Use the immutable formulas, thresholds and denominator minima from the accepted feature spec/bootstrap registry. Make absent observations, invalid reference bindings and undersized/zero cohorts explicit gate failures.

**Rationale**: FR-006/011/012/013 and SC-003/005/006/013 require prospective policy and fail-closed gates; the access/judgment contract reserves policy authorship to the configured owner. Implementation output cannot lower thresholds.

**Alternatives considered**: Defaulting absent counts to zero, accepting post-batch approval, omitting metrics with poor coverage, or deriving a relaxed threshold from measured results would hide missing evidence or create post-hoc policy and are rejected.

## Decision 4: Keep profile eligibility separate from broad reliability

**Decision**: Report a fixture-profile eligibility result after every required metric and contract gate passes. Report general-reliability eligibility separately and deny it unless an assessment carries the separately predeclared population, sample-size rationale, uncertainty interval and held-out batch required by FR-015.

**Rationale**: A 20-case sample is a named profile gate, not a population-level reliability estimate. This is required by the proposal, specs README, FR-015 and SC-010.

**Alternatives considered**: Calling a 20-case pass “reliable,” “validated” generally, or “production ready” is unsupported and rejected.

## Decision 5: Report unavailable cost and measured/enforceable budget separately

**Decision**: Record allowance, enforced limits, observed elapsed work and invocation counts per inquiry. Cost is a numeric observation only when harness telemetry supplies it; otherwise report `unavailable`. Do not make cost a numeric gate because the spec explicitly defers that gate until telemetry and cap enforcement exist.

**Rationale**: FR-004, SC-007/014, proposal I08/I10 and the shared architecture require exact guarantees and unknown-value preservation.

**Alternatives considered**: Treating no telemetry as zero cost, or describing a scheduling cutoff as hard cancellation, would invent guarantees and are rejected.

## Decision 6: Compare like with like and sample disagreement audits prospectively

**Decision**: A comparison is admissible only when fixture IDs, worker/harness/model settings, source versions, task scope, allowances and adjudication protocol match. Disagreement audits record their frozen frame, policy, risk strata, selection rule, adjudicator, severity counts and uncertainty; samples below the predeclared precision target remain descriptive and cannot update policy.

**Rationale**: FR-005/016, SC-008/016 and the edge cases explicitly require fair comparisons and audit limits.

**Alternatives considered**: Post-hoc normalization of different tasks/budgets, discretionary audit selection after seeing outcomes, or changing policy from an unstable sample would invalidate comparison or overstate evidence and are rejected.

## Unresolved questions

None. The registry API and fixture IDs are prerequisite-owned and will be available before implementation by graph order. Runtime harness attribution and actual telemetry are recorded as observed or unavailable, not assumed.
