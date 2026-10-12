# Tasks: Final merged-product convergence and runnable handover

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## scope-t001

- [ ] T001 Inventory all 23 feature `verification.md` paths in `src/analytics/acceptance/99.test.ts` and `docs/build/COMPLETION.md`. Record report presence, actual versus planned/pending content, owning feature, and unresolved limits. A missing report or placeholder is unverified; preserve failures and do not count planned evidence as a pass.

## evidence-inquiry-integration

- [ ] T003 Exercise synthetic/local paths 001 worker boundary, 002 fixed evidence and 003 delegated inquiry through their real producers/controller/inspection routes with injected failures. Preserve primary feature ownership and evidence refs.
  - Files: `src/analytics/acceptance/99.test.ts`, `docs/build/COMPLETION.md`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

## memory-skill-integration

- [ ] T007 Exercise paths 020 answer recall, 004 source memory and 022 skill activation/use through real providers/controller/user inspection routes and injected failures. Preserve primary feature ownership and exact supplied/executed refs.
  - Files: `src/analytics/acceptance/99.test.ts`, `docs/build/COMPLETION.md`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

## recurrence-inference-integration

- [ ] T008 Exercise paths 005 recurrence/use and 006 inference with actual feature producers/controller/inspection routes and injected failures; preserve uncertainty, correction, interpretation and use boundaries and original primary owners.
  - Files: `src/analytics/acceptance/99.test.ts`, `docs/build/COMPLETION.md`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

## scope-t004

- [ ] T004 Exercise the frozen CLI and browser path in `src/analytics/acceptance/99.test.ts` and capture actual IDs, command outcomes, routes, and limits in `docs/build/COMPLETION.md`: live synthetic `gpt-6-luna` / `xhigh` inquiry; `dag analytics answer show`, `trace`, `answers search`, `ledger show`, and `source discover`; automatic answer recall on a later inquiry; exact compatible source-skill version with its recipe actually executed; and a retained failed-required-check trace. Run `pnpm smoke:viewer` and inspect the real answer and evidence-inspector screens. Follow the evaluator API documented by feature 018 after the worker response. Do not expose keys/fault explanations, use canned answers, or claim a path that is missing.

**Independent verification:** `pnpm exec tsx --test src/analytics/acceptance/99.test.ts` and `pnpm smoke:viewer`; also complete the feature-018 evaluator and the live journeys through the documented implementation. Record actual IDs and screen/evidence references.

## runnable-quickstart

- [ ] T005 Complete `docs/QUICKSTART.md` with prerequisites, synthetic fixture setup, exact runnable command lines and required arguments, observed answer/trace screens, search/recall, ledger, source discovery and second-inquiry reuse, and failed-check view. State which commands and screens were exercised and show actual output/IDs and limits; label unavailable capabilities as unavailable rather than planned runnable flows.

**Independent verification:** `pnpm build` and `pnpm exec tsx --test src/analytics/acceptance/99.test.ts`; follow the quickstart against the local fixture and compare its commands, outputs, and screen descriptions with the observed run.

## scope-t002

- [ ] T002 Extend `src/analytics/acceptance/99.test.ts` to derive and assert the exact 234 FR, 219 SC, 115 source-ordered edge, 61 T-case primary-owner, 23 report, and eight integrated-path sets from the accepted specs/maps/registers. Require a machine-checkable `COMPLETION.md` evidence matrix with exactly one row per expected FR, SC, edge, T case, and path; verify owner and source report, outcome, and actual evidence reference; reject missing/duplicate/extra rows, missing reports, pending/TODO-only rows, unresolved evidence links, or incomplete denominators. Run `python scripts/audit_spec_coverage.py`; do not edit specs, maps, reports, or criteria.

**Independent verification:** `python scripts/audit_spec_coverage.py` and `pnpm exec tsx --test src/analytics/acceptance/99.test.ts`. The structural script alone does not establish behavior. Any missing report or evidence row remains a failing convergence result.

## release-handover

- [ ] T006 Run `pnpm build`, `pnpm test`, `uv run --project python pytest`, `python scripts/audit_spec_coverage.py`, `pnpm smoke:viewer`, and the focused 99 acceptance test; run the complete feature-018 evaluator and documented live user journeys. Write `docs/build/COMPLETION.md` with actual exits, all FR/SC/edge/T/path outcomes and evidence, report inventory, unchanged evaluator thresholds and denominators, observed metrics, cost as observed or unavailable, demo artifact references, runtime/harness and access/holdout limits, and every unresolved failure. Fix only Node 99-owned test or handover defects here. For an upstream implementation defect, record evidence plus owning feature/task and fail convergence; the coordinator must arrange a separate bounded repair with that owner, after which affected checks are rerun. Do not patch unowned production files, relax acceptance, hard-code answers, or claim completion while a gate is missed.

**Independent verification:** Repeat the full release command set, evaluator, live journeys, and browser inspection from the merged integration checkout. Completion status must agree with retained test, evaluator, browser, CLI, and report evidence; cost is non-gating when telemetry is unavailable.

## Dependencies

| Unit | Depends on |
| --- | --- |
| scope-t001 | Completed feature prerequisites |
| evidence-inquiry-integration | scope-t001 |
| memory-skill-integration | evidence-inquiry-integration |
| recurrence-inference-integration | memory-skill-integration |
| scope-t004 | recurrence-inference-integration |
| runnable-quickstart | scope-t004 |
| scope-t002 | runnable-quickstart |
| release-handover | scope-t002 |
