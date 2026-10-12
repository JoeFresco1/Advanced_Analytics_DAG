# Quickstart: Verify Skill Capture

## Prerequisites

- Node.js 20+ and the repository's installed `pnpm` dependencies.
- Accepted providers/contracts from 003, 009, 014, 015, and 020, plus the frozen synthetic 48-signal fixture and evaluator API from 18-bootstrap.
- Synthetic/local fixture inputs only. Reference labels and expected answers stay outside the capture provider's worker context.

## Validation

From the repository root:

```powershell
pnpm build
```

The feature acceptance checks are runnable independently of unrelated feature tests:

```powershell
pnpm exec tsx --test --test-name-pattern=capture-US1 src/analytics/acceptance/021.test.ts
pnpm exec tsx --test --test-name-pattern=capture-US2 src/analytics/acceptance/021.test.ts
pnpm exec tsx --test src/analytics/acceptance/021.test.ts
```

The final feature unit also runs `pnpm build` before the complete 021 acceptance file.

## Scenarios exercised by the feature acceptance path

1. A synthetic ordinary follow-on delegated question carries feedback about a completed answer in its 003 terminal-intent/user-request ref. At that question's `beforeFinalize`, after the 020 answer card is committed, natural-language method endorsement produces one evidence-linked, scoped candidate, while answer-only praise routes to the 020 answer annotation path.
2. Two independent completed inquiries, a predeclared threshold in an 014 candidate source package read through 015, and a validated correction with later reuse each produce the correctly typed candidate.
3. Repeated identical capture is deduplicated with a visible reason; changed procedure content creates a linked revision.
4. Unvalidated/significant-only results do not create activation-eligible guidance. An owner-definition conflict names the definition/owner and stays `candidate`.
5. Holdout outcomes remain scoped answer/evidence references with exposure provenance. Single-role or single-period support stays narrowly scoped.
6. A checkpoint at its frozen work/time limit stops, records the reason, performs no retry, and leaves finalization capacity available.
7. The 18-bootstrap evaluator API adjudicates all 48 frozen signals after capture; the report shows proposal IDs, TP count, proposal count, positive count, precision, recall, and duplicate count. Missing labels/denominators or any frozen gate failure rejects the profile.

The quickstart describes expected checks; this task-planning stage does not run them. The final task updates `verification.md` with actual outputs and evaluator measurements.
