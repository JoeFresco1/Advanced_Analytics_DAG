# Final convergence evidence contract V1

This contract defines the evidence inventory and handover checks for graph
node 99. It is a planning contract; it adds no analytics API, changes no
feature acceptance rule, and does not authorize edits outside the three node
99 graph outputs.

## Authoritative inputs

- Feature identity, FRs, SCs, and edge order: `specs/[0-9][0-9][0-9]-*/spec.md`.
- FR-to-primary-check relation: `docs/specs/fr-verification-map.md`.
- Edge owner/check relation and order: `docs/specs/edge-verification-map.md`.
- T01–T61 definition and exact one-owner partition:
  `docs/specs/advanced-analytics-dag-proposal.md` and
  `docs/specs/traceability-audit.md`.
- Eight integrated path IDs: `specs/README.md` and
  `docs/build/acceptance-plan.md`.
- CLI, viewer, evaluator boundary, and file ownership:
  `docs/build/interfaces.md`, `docs/build/ARCHITECTURE.md`, and
  `docs/build/graph.json`.
- Actual feature outcomes: `specs/<feature>/verification.md` and its
  referenced test/command/evaluator/browser evidence.

All sources above are read-only. `python scripts/audit_spec_coverage.py`
checks structural FR/edge references; the node-99 acceptance suite adds the
SC, report, evidence, T-owner, and integrated-path completeness checks.

## Completion matrix

`docs/build/COMPLETION.md` includes a parseable Markdown table with these
columns:

```text
Kind | ID | Feature/owner | Primary check | Outcome | Source report | Evidence reference
```

`Primary check` may be empty for an item with no distinct mapping. The table
contains exactly one row for each keyed item:

- `(FR, featureKey, FR-id)` for all 234 functional requirements;
- `(SC, featureKey, SC-id)` for all 219 success criteria;
- `(EC, featureKey, EC-id)` for all 115 edge cases, numbered in each spec's
  source order and matching its edge-map row;
- `(T, primaryOwner, T-id)` for all 61 T cases, with exact owner from the
  ownership audit;
- `(PATH, milestone, milestone-id)` for 001, 002, 003, 020, 004, 022, 005,
  and 006.

The completion report also contains a separate 23-row feature report inventory
keyed by feature ID and path. Each feature verification report must give actual
outcomes/evidence for every FR, SC, and EC in its spec and each T case it
primarily owns. A missing file, omitted item, planned/pending/TODO-only row, or
missing actual evidence is a completeness failure.

The exact outcome tokens for this **handover matrix only** are `pass`, `fail`,
`blocked`, `not_run`, and `unavailable`. Only `pass` satisfies a release
criterion. `fail`, `blocked`, `not_run`, and `unavailable` remain visible and
make the corresponding release gate fail. These report tokens do not replace
the canonical analytics inquiry, check, Claim, predictive, comparison,
validity, reproduction, skill, or authorization vocabularies. For example, a
test can pass by confirming that a legitimate negative result remains
unsupported and visible.

## Evidence reference requirements

Every matrix row names its feature report and a nonempty reference to observed
evidence: a command/test run artifact with actual exit/output, committed
inquiry/result/check/skill IDs, an evaluator batch record with measured
denominators, or an inspected browser route/screen artifact. Use durable IDs
or resolvable repository-relative artifact links as supplied by the actual
implementation. The acceptance test verifies local links/IDs against retained
evidence; it does not accept a task description or planned assertion as proof.

If work was not run, preserve the actual not-run/missing observation and its
reason; do not fabricate a command result or link. Such a row can make the
inventory honest, but it cannot pass completeness or release acceptance.
Evaluator rows preserve the frozen fixture-lock and threshold references,
numerator/denominator, observed value, harness/model settings, and cost as
observed or unavailable. No threshold or expected answer is read from or
written into worker context.

## Machine-checkable assertions

`src/analytics/acceptance/99.test.ts` will:

1. Read the accepted source documents and derive exact IDs and owners; verify
   the expected totals 23 / 234 / 219 / 115 / 61 / 8.
2. Compare each derived set with the completion inventory/matrix and reject
   omissions, duplicates, extras, out-of-order edge IDs, or owner drift.
3. Require 23 feature reports and the correct feature-local FR/SC/EC IDs plus
   exact primary T IDs in each report; reject pending/planned placeholders as
   actual evidence and require resolvable evidence references.
4. Require every completion outcome to agree with its feature report and
   underlying evidence. A failed/missing owner feature, threshold, live path,
   or browser observation remains a failed/not-run gate rather than a
   fabricated pass.
5. Verify the eight path rows refer to actual producer, controller consumer,
   user-visible outcome, and injected-failure evidence. Verify the explicit
   CLI journeys for answer recall, executed source-skill reuse, and retained
   failed-check trace.

The completion matrix reports every outcome, but a structurally complete
matrix is not itself product acceptance. The final release is eligible only
when the unchanged feature/evaluator gates pass and no required observation
is missing.

## Frozen user and release actions

Use only the exact commands and argument shapes in
`docs/build/interfaces.md`: `dag analytics ask`, `answer show`, `trace`,
`answers search`, `ledger show`, `source discover`, then a second `ask`.
Record actual arguments, generated IDs, output, and browser route. Run
`pnpm smoke:viewer` and inspect the rendered answer and evidence trace in a
real browser. The real analytical ask uses synthetic/local source data with
`gpt-6-luna` and `xhigh`.

Run independently from the repository root:

```powershell
pnpm build
pnpm test
uv run --project python pytest
python scripts/audit_spec_coverage.py
pnpm smoke:viewer
pnpm exec tsx --test src/analytics/acceptance/99.test.ts
```

Run the complete evaluator through the API/entrypoint actually provided by
feature 018; do not invent a CLI. Run source-skill reuse, automatic answer
recall, and a failed required-check journey with local synthetic fixtures.
The worker receives neither evaluator keys nor planted-fault explanations.

## Ownership and failure handling

Node 99 may change only `src/analytics/acceptance/99.test.ts`,
`docs/build/COMPLETION.md`, and `docs/QUICKSTART.md`. An upstream production
defect is recorded with actual evidence, its graph-owning feature/task, and a
failed convergence outcome. The coordinator assigns a separate bounded repair
to that owner; after it is complete, rerun the affected owner checks and final
convergence checks. T006 does not make ad hoc cross-owner production edits.
