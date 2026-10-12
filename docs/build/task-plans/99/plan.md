# Final Merged-Product Convergence and Runnable Handover

**Graph node:** 99  
**Prerequisites:** 018, 005, 006, 020, 022  
**Graph-owned outputs:** `src/analytics/acceptance/99.test.ts`, `docs/build/COMPLETION.md`, `docs/QUICKSTART.md`

## Summary

Converge the merged product at its user-facing boundaries after all five graph prerequisites are complete. Audit all 23 feature verification reports and preserve the accepted evidence for 234 FRs, 219 SCs, 115 ordered edge cases, all 61 exact primary T-case owners, and the eight integration paths. Missing reports, planned or pending rows, skipped checks, missing denominators, failed thresholds, and unavailable observations remain explicit failures or limits.

The acceptance test performs a machine-checkable completeness audit. It derives the expected IDs from the 23 feature specs, FR and edge maps, T-case ownership audit, and accepted integration-path register. `COMPLETION.md` contains one evidence row for each expected FR, SC, edge, T case, and integration path, plus a 23-feature report inventory. Each claimed pass points to observed acceptance evidence; a not-run, missing, or unavailable item cannot be reported as passed. The suite checks the exact sets, owners, counts, evidence references, duplicate/missing/extra rows, and report contents. This is separate from `python scripts/audit_spec_coverage.py`, which verifies structural spec/map references but does not establish implementation behavior.

Exercise the eight user paths as producer-to-controller-to-viewer journeys, use the frozen CLI with synthetic/local fixtures and a live `gpt-6-luna` / `xhigh` question, verify automatic answer recall and an exact compatible source-skill version whose recipe is executed, and preserve a failed required-check trace. Inspect the real answer and trace browser screens. Run the frozen feature-018 evaluator without giving its keys, relevance labels, or planted-fault descriptions to the worker. Do not supply answers from fixture constants or treat process success as analytical support.

`docs/QUICKSTART.md` records commands and user screens that were actually exercised, including observed IDs/routes and limits. `docs/build/COMPLETION.md` records command exits, all FR/SC/edge/T/path outcomes and evidence, evaluator metrics and denominators, cost as observed or unavailable, demo artifact references, runtime/harness details, access/holdout limits, and unresolved failures. A passing frozen profile supports only the named profile; it does not establish broad reliability.

## Design inputs and boundaries

- Product semantics remain governed by proposal revision 2.6, constitution 1.4.1, `docs/specs/state-vocabularies.md`, and `docs/specs/access-and-judgment-contract.md`. Keep execution, evidence, checks, claim state, current validity, authorization, and inquiry outcome separate. Negative, null, failed, inconclusive, conflicting, and incomplete work remain visible.
- `docs/build/graph.json`, `docs/build/interfaces.md`, and `docs/build/acceptance-plan.md` define exact file ownership, frozen CLI actions, evaluator isolation, and the eight paths. The milestone index is `specs/README.md`. Feature specs, requirement-quality checklists, thresholds, labels, reference answers, and fixture locks remain read-only.
- Use synthetic/local fixtures only. When local filesystem access can bypass product surfaces, describe withholding as workflow-level; do not claim enforced holdout or source isolation.
- Node 99 owns only the three graph outputs listed above. Its tests consume feature-owned producers and viewers without taking ownership of their domain behavior. Do not edit `dag.run.json`, graph or architecture files, specs/checklists, evaluator locks, package manifests, or feature-owned thresholds.
- Do not invoke the DAG runner, change branches, merge, push, commit, or publish. Build orchestration owns those operations.
- Convergence is an audit, smoke, and handover owner. T006 may correct defects in Node 99's own acceptance test or handover documents. If an upstream implementation defect is found, record the evidence, owning feature and task, and mark convergence failed. The coordinator must arrange a separate bounded repair with that owner; T006 must not patch unowned production files or claim success before the repair and affected checks are complete.
- The observed planning baseline has nine of 23 feature `verification.md` reports, all marked planned/pending. This is inventory evidence only. The final implementation pass must re-inventory all 23 and require actual results.

## Acceptance approach

1. Inventory the exact 23 feature report paths. Compare every report with its feature FRs, SCs, ordered edge cases, and owned T cases. Planned/pending text is not actual acceptance evidence. Preserve each owning feature's recorded failure and limits; a missing report is unverified and fails completeness.
2. In `99.test.ts`, derive and compare the full FR, SC, edge, T-owner, report, and path registers. Require a one-to-one `COMPLETION.md` evidence matrix keyed by feature and ID (or global T/path ID), with no missing, duplicate, or extra row. Each row includes an outcome and actual evidence reference; local evidence paths/IDs resolve, and missing/placeholder evidence fails. Assert the accepted totals: 23 reports, 234 FRs, 219 SCs, 115 edge cases, 61 primary T owners, and 8 integrated paths.
3. Exercise these integration paths named in `specs/README.md` and `docs/build/acceptance-plan.md`: 001 worker boundary; 002 fixed evidence; 003 delegated inquiry; 020 automatic answer recall; 004 source memory; 022 skill activation and later use; 005 recurrence and use; 006 inferential profile. Verify a producer, controller consumer, user inspection path, and injected failure for each applicable slice without replacing its feature-owned acceptance.
4. Exercise the frozen actions in `docs/build/interfaces.md`: `dag analytics ask`, `answer show`, `trace`, `answers search`, `ledger show`, `source discover`, and the second `ask`. Capture actual IDs and evidence. Include automatic prior-answer recall, execution of the pinned compatible source-skill recipe, and a failed-check trace. Run `pnpm smoke:viewer` and inspect the real rendered answer and trace screens; do not invent route names or evaluator commands.
5. Complete all commands independently from the repository root, then use the documented feature-018 evaluator API and repeat the live synthetic paths. Record measured evaluator counts, denominators, unchanged thresholds, cost telemetry or its unavailability, artifacts, runtime, and limits. No missing observation or missed gate is converted into a pass.

## Frozen release commands

Run each command independently and record its actual exit and relevant output:

```powershell
pnpm build
pnpm test
uv run --project python pytest
python scripts/audit_spec_coverage.py
pnpm smoke:viewer
pnpm exec tsx --test src/analytics/acceptance/99.test.ts
```

The full evaluator and live CLI journeys use the implementation and invocation documented in `docs/build/interfaces.md`, `docs/build/acceptance-plan.md`, and feature 018's `quickstart.md`/verification report. Record that actual invocation and the generated IDs; do not invent an evaluator CLI. The final report must distinguish the structural map audit from behavioral acceptance and state any blocked or failed gate.
