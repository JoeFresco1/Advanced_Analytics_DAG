# Quickstart: Verify Answer Cards and Automatic Recall

All scenarios use feature-scoped synthetic/local fixtures. Do not use remote or confidential source data. Run this guide only after 003, 009, 011, 019, and 023 are implemented. The feature tests keep evaluator relevance labels and reference answers outside worker context.

## Build and deterministic acceptance

From the repository root:

```text
pnpm build
pnpm test
```

The 020 cases live in `src/analytics/acceptance/020.test.ts`. Process success is not an analytical outcome: the fixture assertions check card states, evidence bindings, access filtering, per-card relevance reasons, query outcome, exposure, and the frozen metric denominator.

## Search the 99-file library

1. Load the local fixture with 99 mixed CSV/workbook files, including duplicate bytes, unknown provenance, several exploratory artifacts, and a committed answer card.
2. Search by the question and period without naming a file.
3. Confirm the scoped answer appears with stable artifact and ledger-timeline links. Confirm the artifact inventory still contains intermediates and legacy files without turning them into answer cards from filenames.
4. Search a negative and an inconclusive answer. Confirm uncertainty remains visible and neither is mislabeled as a failed process.

## Verify ranking and no-match

Run the frozen evaluator against at least 150 cards and the 20 annotated queries with pins disabled. Confirm Hit@3 is at least 18/20, reported as qualifying queries divided by 20, and every returned card has its relevance class and mismatch reason. Run the five frozen no-match queries, including an empty corpus; confirm zero false direct answers and no restricted card metadata. Do not edit query labels or acceptance thresholds after seeing results.

## Pin and revisit important work

1. Finalize an inquiry containing the explicit user instruction “remember this analysis”. Confirm the resulting answer card and pin event are durable before completion is acknowledged.
2. Rebuild the index, then confirm the stable card ID, pin, annotation, evidence refs, and timeline link remain.
3. Correct or redact a source. Confirm the historical card remains available with the canonical current-validity/availability state and a safe correction link.
4. Open project home without a search query. Confirm recent, pinned, and unresolved accessible cards appear, including a negative finding and an unresolved inquiry.
5. Open an answer and follow its ledger link. Confirm every admitted attempt, method, outcome state, evidence link, and next-action reason is visible.

## Demonstrate automatic recall with a later live worker

Use the feature fixture's local project and synthetic source paths printed by the acceptance setup. Run the first inquiry through the frozen CLI ask path with model `gpt-6-luna` and reasoning `xhigh`; finalize it so the card and ledger are committed. Then start a second inquiry in the same local project with a related analytical question that does not mention or hint at the prior inquiry. Confirm orientation receives the accessible prior card, includes scope/current validity/evidence refs, records retrieval and outcome exposure before context is supplied, and records the worker's later use or rejection reason. Confirm 023's prior-attempt assessment was handed to 020 once, 020 performed exactly one retrieval event, and a different cohort or stale source was not reused as current evidence.

The ask invocation uses the shared command contract:

```text
dag analytics ask --project <fixture-project> --question <question> --source <local-synthetic-source> --model gpt-6-luna --reasoning xhigh
```

Record the actual harness/version, context and source digests, inquiry/card/retrieval refs, budget, and result in `verification.md`. The worker receives no reference-answer or relevance-label data. Any local shell-readable fixture remains labeled workflow-level; do not claim enforced holdout isolation unless the host boundary actually provides it.

## Inspect the answer and history

```text
dag analytics answers search --query "churn last quarter" --period <range>
dag analytics answers home
dag analytics answer show --inquiry <id> --open
dag analytics ledger show --inquiry <id>
```

The answer route shows the concise scoped story, uncertainty, validity, and evidence links. Its ledger-timeline link opens all admitted attempts. The home command requires no query and returns only currently accessible cards.
