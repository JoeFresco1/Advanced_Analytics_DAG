# Tasks: 006 â€” Two-group inferential renewal-rate profile

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## risk-difference-calculator

- [ ] T001 [P] [US1] Add feature-local Plan/request builders and synthetic customer-row fixtures with known-effect, null, and low-power calculator assertions through the Python dispatcher in `src/analytics/acceptance/006.test.ts`; validate requests against 001's generated inferential schema and keep evaluator keys out of worker context.
- [ ] T002 [P] [US1] Implement the authoritative `compute(request)` profile in `python/analytics_worker/inferential.py` using 001's `InferentialRequestV1`; compute signed `p_B - p_A`, the declared Newcombe-Wilson or cluster-aware interval, customer numerators/denominators, and assumptions under the frozen payload, leaving limitation and supported-presentation state to the existing check/Claim path and without TypeScript numeric calculations.

## scope-t003

- [ ] T003 [US1] Export `computeAnalytics(request, services)` and `analyticsExtensionV1` with extension ID `inferential-compute` from `src/analytics/inferential.ts`; route the inferential mode through the frozen 003 dispatch seam and shared binding adapter.

## method-applicability

- [ ] T005 Implement method applicability in python/analytics_worker/inferential.py and src/analytics/inferential.ts for customer-level reduction, dependence/cluster uncertainty, small groups, missing/changing categories, representativeness, name-derived labels and unsupported methods. Follow the frozen 006 fault precedence and 010 policy limits. Add valid controls and these fault cases through the existing Python/TypeScript dispatcher.

## selection-interpretation

- [ ] T007 Implement outcome-selected subgroup, comparison-family and repeated-look interpretation in src/analytics/inferential.ts and python/analytics_worker/inferential.py using committed feature-016 exposure/look evidence. Preserve association-only conclusions and scope/limitations; test no-selection controls plus selected subgroup, repeated look, missing evidence and unsupported causal claims without changing the numeric estimand.

## scope-t004

- [ ] T004 [US2] Extend `src/analytics/acceptance/006.test.ts` with the ordered fault fixtures for customer dependence, small groups, missing/changing categories, representativeness, name-derived labels, outcome-selected subgroups, repeated looks, unsupported methods, and causal overreach; include T54/T55/T58 and the live browser answer/trace path.

## fault-fixtures-and-browser-evidence

- [ ] T006 Run the complete feature acceptance commands and record actual command/exit evidence, fixture IDs, three causal-wording run outcomes, calculator and browser trace observations, FR/SC/edge-case coverage, limitations, and any failed or blocked result in `specs/006-inferential-profile/verification.md`.

## Dependencies

| Unit | Depends on |
| --- | --- |
| risk-difference-calculator | Completed feature prerequisites |
| scope-t003 | risk-difference-calculator |
| method-applicability | scope-t003 |
| selection-interpretation | method-applicability |
| scope-t004 | selection-interpretation |
| fault-fixtures-and-browser-evidence | scope-t004 |

## population-applicability

- [ ] T008 Complete original T005 by enforcing missing/changing category definitions, representativeness limitations and name-derived-label restrictions through the existing inferential dispatcher and 010 checks. Consume the completed customer-unit/uncertainty handling; do not change the numeric estimand or classify unsupported causal claims as evidence. Test valid known-category/declared-population controls plus unknown/changed meaning, biased scope and name-derived labels under the frozen 006 precedence.

## Capability amendment — 2026-10-10

The execution manifest owns amended unit order and review scope. The preserved original task text remains a whole-feature obligation; split units jointly satisfy it. New corrections have their own checklist IDs.
