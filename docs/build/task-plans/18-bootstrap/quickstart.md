# 18-bootstrap planning quickstart

This guide defines validation for the implementation handoff. It does not run
product implementation during task-plan repair.

## Prerequisites

- Graph prerequisite `001` has completed its worker-context, principal/grant,
  and neutral-execution outputs.
- The configured owner and approval timestamp for initial metric thresholds
  are supplied through feature 010's policy contract before any evaluated
  batch starts. The initial lock may record that approval as pending until
  then; do not fill a fabricated actor or timestamp.
- All fixture data is synthetic and local. The local shell profile is
  `workflow-withheld; access isolation not enforced`.

## Incremental unit validation

Run the listed command after its manifest unit is implemented. Pattern names
select focused tests already in the shared bootstrap suite.

| Unit | Commands | Expected result |
| --- | --- | --- |
| `fixture-package` | `pnpm build`; `pnpm exec tsx --test --test-name-pattern=fixture-package src/analytics/acceptance/18-bootstrap.test.ts` | Same generator seed yields identical case IDs/digests; the worker/reference boundary and exact cohort sizes validate. |
| `reference-registry` | `pnpm build`; `pnpm exec tsx --test --test-name-pattern=registry src/analytics/acceptance/18-bootstrap.test.ts` | Exact initial thresholds and owner gate validate; exposure, disagreement, comparative, append-only evidence, and contribution cases pass. |
| `fixture-lock-report` | `pnpm build`; `pnpm exec tsx --test --test-name-pattern=fixture-lock src/analytics/acceptance/18-bootstrap.test.ts` | Initial lock IDs/digests, denominators, frozen metrics, pending approval, and workflow-withheld boundary match explicit literals. |
| `bootstrap-acceptance` | `pnpm build`; `pnpm exec tsx --test src/analytics/acceptance/18-bootstrap.test.ts` | Complete bootstrap acceptance passes and emits no product release or broad reliability assessment. |

## Contribution lifecycle validation

1. Prepare a synthetic contribution for a configured feature owner. Include new
   fixture IDs and matching content digests, independent expected-outcome and
   rubric digests, the exact parent lock ref, and approval time.
2. Register it before the target batch begins. Confirm the result is a new
   immutable child lock revision and that the parent, existing expected
   outcomes, and metric thresholds retain their digests.
3. Attempt an unauthorized owner, changed content under an existing ID,
   missing expected outcome, and approval/admission at or after batch start.
   Each must be rejected with an attributable reason.
4. Pin a batch to the contributed child revision. Create a later revision and
   confirm evaluator selection for the first batch still returns exactly its
   pinned fixture and reference set.
5. Pass synthetic retrieval-card, source-skill, and cached-summary additions
   through the 001 context serializer. Confirm key/fault canaries are absent
   from packet bytes and any packet containing a canary is rejected.

## Frozen cohort assertions

- Tabular profile: at least 20 evaluation-role questions; at least 20 each for
  presented assertions, planted faults, valid claims, displayed numerical
  assertions, and admitted attempts.
- Retrieval: exactly 150 cards, 20 annotated relevance queries, and five
  no-match queries including an empty-corpus query; the no-match expected
  false-direct-answer count is zero.
- Import: exactly 99 mixed CSV/workbook files with stable path identity,
  duplicate content at distinct paths, and missing-provenance examples.
- Capture: exactly 48 signals with 32 positives and 16 negatives; duplicate
  proposals count as incorrect proposals.
- Metric registry: ten exact feature 018 gates; cost is observed or
  unavailable and non-gating.

A passing bootstrap validates frozen inputs, denominator definitions,
adjudication separation, and registry behavior only. Feature 018 later runs
whole-product evaluations and owns release eligibility.
