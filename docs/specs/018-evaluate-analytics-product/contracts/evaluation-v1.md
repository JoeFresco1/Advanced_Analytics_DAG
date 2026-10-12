# Evaluation Service Contract v1

This is a feature-local service contract. The bootstrap fixture registry and
reference evaluator remain authoritative for fixture definitions, expected
answers, fault labels, initial metric entries and immutable fixture digests.
Feature 018 consumes that API and must not edit its registry, lock, policy or
expected values.

## Inputs

`EvaluationBatchRequestV1` carries the frozen fixture/profile references, the
registry and evaluator versions, allowed source/grant profile, model and
reasoning settings, harness/environment identity, per-question enforceable
allowances, and a pre-batch reference digest snapshot. It identifies a real
worker/controller caller capable of producing committed inquiry and evidence
records. It contains no expected answers, fault descriptions, relevance labels
or adjudication decisions.

`MetricRegistryV1` is read-only input. Every gate metric resolves its formula,
numerator, denominator, direction, minimum cohort, threshold, profile, owner
approval actor (the configured project owner under feature 010 FR-008),
approval time and exact fixture/evaluator refs before the batch starts.

## Worker/evaluator boundary

1. Resolve the fixture's question and permitted data/grant inputs.
2. Construct and digest the actual context packet without evaluator-only
   references. Run the production question-to-answer path with the recorded
   settings and allowance.
3. Commit/read the actual answer and evidence records; record their IDs and
   digests. Record context exclusion evidence before scoring.
4. Only after the worker has returned, use the evaluator-only registry reader
   to load the pinned rubric, reference answer and planted-fault labels.
5. Verify all pre-run reference digests still match. A key leak, digest change,
   unpinned version, or inaccessible required metric invalidates the batch and
   blocks profile eligibility.

The local profile reports `workflow-withheld; access isolation not enforced`
unless the harness has demonstrated a stronger boundary. The context exclusion
audit is zero-tolerance for answer-key and fault-description bytes; a clean
audit is not represented as filesystem isolation.

## Outputs

`EvaluationBatchV1` reports one observation for every expected fixture and
retains original run/inquiry/evidence references, worker/harness/model/prompt
settings, source version, grant profile, allowance, actual work, elapsed time,
observed or unavailable cost, and context/reference integrity evidence.

`MetricObservationV1` includes frozen metric identity, exact numerator,
denominator, value or explicit unavailable reason, threshold, minimum, unit,
profile, case IDs and evidence references. Each formula follows the accepted
feature specification and locked registry. No metric result is inferred from
task or process exit counts.

`ReleaseAssessmentV1` includes each calculation and gate result, exact failures
and evidence, `fixtureProfileEligible`, and a separate general-reliability
assessment. A 20-case pass can only set the named frozen fixture profile
eligible. The general assessment remains ineligible without the separately
predeclared population, sample-size rationale, interval and held-out batch.

`ComparisonV1` carries both batch references and proves identical task/fixture
constraints, source versions, model/harness settings, allowances and
adjudication protocol before displaying a difference. A mismatch returns a
failed validation with the exact differing fields.

`AuditSampleV1` records frame, policy, risk strata, selection rule/seed,
adjudicator, disagreement severity, numerator, denominator and uncertainty.
A sample below the frozen precision target is descriptive only and cannot
change any threshold.

## Failure behavior

- Registry/approval/reference/context violations invalidate the batch.
- A required metric below its minimum or with an unavailable observation
  blocks release and reports exact counts/references.
- Missing cost is `unavailable`, not zero. Cost is not a numerical gate in
  this profile until telemetry and an enforceable cap are approved.
- A failed analytical check or unfavorable worker answer remains scored
  evidence; it does not trigger a retry for a favorable answer.
- An always-block worker may have high fault recall but fails the valid-claim
  false-block gate.
- A comparison mismatch cannot be normalized into a pass.
- A small disagreement sample remains descriptive and cannot change policy.

## Accepted metric authority

The initial metric IDs, exact formulas, thresholds and minimum denominators are
the immutable table in `specs/018-evaluate-analytics-product/spec.md` and the
bootstrap fixture lock. The implementation reads and validates them; it does
not copy, tune, infer, or override them. In particular, this includes the
20-question answer-quality denominator, 20 presented assertions for
unsupported promotion, 20 planted faults, 20 valid claims for false-blocking,
20 numerical assertions for trace completeness, 20 questions for budget
completion, 20 retrieval queries against the 150-card fixture, 20 ledger
attempts, 10 signal proposals, and all 32 positive candidate signals.
