# Quickstart: Verify Data Identity and Measurement

This guide is for the implementation acceptance unit after dependencies 001, 009, and 18-bootstrap are available. It uses deterministic synthetic/local fixtures only and does not read external customer data.

## Prerequisites

- The shared Pydantic descriptive contracts, generated schemas, and Python dispatcher from 001 are present.
- The 009 stage and immutable publication services are available.
- The shared 18-bootstrap fixture/evaluator API is available; its fixture lock and expected references are unchanged.
- The local Python environment is installed from the 001-owned lockfile.

## Run the acceptance path

From the repository root, run:

- pnpm build
- pnpm exec tsx --test src/analytics/acceptance/007.test.ts
- pnpm test

The feature fixture must invoke the real Python analytics_worker descriptive calculator through the shared dispatcher and the TypeScript mode facade. Do not mock a completed numeric payload. The fixture's expected/reference data is not included in the worker request.

## Scenarios and expected evidence

1. Measure a fixed synthetic customer population. Follow its displayed denominator to the exact source-version ref and every ordered transformation. Verify the result includes statistic, units, population, period/timezone, group meaning, missingness, exclusions, and denominator definition.
2. Join customer and event fixtures with a declared one-to-many grain change. Row count increases, distinct customer count and customer denominator reconcile, and the measurement remains usable. Repeat with duplicate-key over-expansion and an unexplained population exclusion; the owning finding and expected/observed counts remain visible and support is blocked.
3. Import a legacy fixture with unknown prior grain/cardinality. Confirm those facts remain unknown. Consume it only through a new declaration of input grain/key cardinality, and validate the actual Python-produced counts before any dependent support.
4. Compute count, sum, mean, and proportion from fixture rows. A grouped result carries the exact category-definition ref and scope. Excluded/missing observations remain separate. For a proportion with zero denominator, the result reports denominator 0, no numeric proportion, and a reason.
5. Compare two acquisitions with the same schema but changed unit, timezone, key meaning, or category definition. The semantic refs differ and an unqualified comparison is blocked. A query-time-only acquisition has explicit unknown consistency and no exact-snapshot claim.
6. Seed a late event and an immature target label. Preserve source/event time, processing time/watermark, maturity rule/cutoff and affected count. Exclude immature labels or mark them unavailable; do not count them as negative labels.
7. Seed an expected/actual source-role or grant mismatch and a group without semantic definition. Deny or block access before protected values are returned, expose the owning check and access-safe reason, and block only the affected supported claim.

## Review evidence

The final verification report records actual command exit/results, the evidence references produced, and an FR/SC/Edge mapping. A successful process is recorded separately from each analytical finding. Test results prove only the declared local failure classes; they do not claim host-level filesystem isolation, universally correct business definitions, or a global freshness policy.

