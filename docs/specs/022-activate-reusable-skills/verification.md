# Verification Plan: Activate and Reuse Learned Skills

**Status**: Planned at task-plan handoff; no implementation or product tests
have been run in this stage. Task T012 replaces planned entries with actual
commands, outcomes, and evidence links.

**Acceptance command**: `pnpm test` (runs the repository build followed by the
Node test suite, including `src/analytics/acceptance/022.test.ts`).

Use only the accepted synthetic/local fixture registry. Preserve the accepted
specification, checklist, labels, policy thresholds, and reference answers.
No outcome is inferred from task/process success alone.

## Requirement evidence map

| Requirement | Planned observable evidence in `src/analytics/acceptance/022.test.ts` | Task |
| --- | --- | --- |
| FR-001 | Method/rule and source activation records bind exact candidate evidence, type/version, scope predicate, audience, configured authority, independent test, conflict assessment, and reason; missing bindings keep the candidate. | T002, T003, T014, T015 |
| FR-002 | Worker-authored self-tests yield zero method activations; an independently checked, preapproved low-risk method activates; authority-sensitive changes wait for the configured owner. | T001, T003, T008 |
| FR-003 | Concrete configured-owner approval supplies authority once with zero duplicate prompts/events after checks pass. | T007, T008 |
| FR-004 | A single-study outcome retains provenance/exposure and never becomes an unscoped authoritative instruction. | T005, T011 |
| FR-005 | Exact current version is supplied only when lifecycle, audience, current access, freshness, and declared scope match; source reuse calls 015's normative compatibility predicate. | T004, T005, T011, T015 |
| FR-006 | Supplied refs and actually executed examples have separate use records; prior outcomes have exposure refs. | T006, T011, T015 |
| FR-007 | Stale/revoked versions do not guide new inquiries; the earlier inquiry still pins the exact historical version. | T009, T010, T015 |
| FR-008 | Source-unit/access-role changes stale affected versions; configured-authority withdrawal revokes and prevents new supply. | T009, T010 |

## Success-criteria evidence map

| Criterion | Planned observable evidence | Task |
| --- | --- | --- |
| SC-001 | A method candidate activates under the independent policy gate; a second compatible inquiry receives and actually uses the exact active method version without a save/recreate prompt. | T005, T006, T011 |
| SC-002 | Seeded conflicts remain candidate until configured authority resolves them; older definition versions remain addressable. | T007, T008, T010 |
| SC-003 | A prior outcome is excluded from unrelated guidance or supplied only with status/provenance/exposure. | T005, T011, T015 |
| SC-004 | One concrete configured-owner approval passes authority once and causes no duplicate approval prompt. | T007, T008, T011 |
| SC-005 | Worker self-test alone produces zero activation; an independently validated eligible method activates; every changed-definition, expanded-audience, access-rule, and release-policy fixture remains candidate pending its configured owner. | T001, T003, T008, T014 |
| SC-006 | Withdrawal makes the active version revoked and unsupplied; changed-unit/access-role fixtures stale only affected guidance while historical uses remain visible. | T009, T010 |
| SC-007 | Required activation fields are asserted for method and source versions; missing-field variants cannot activate; the source version is committed as immutable active state only after independent validation and authority. | T002, T003, T012, T014, T015 |
| SC-008 | Wrong role, period, source, or declared scope yields no current guidance; compatible versions have attributable use; source compatibility comes from 015. | T004, T005, T011, T015 |
| SC-009 | A single-study Result proposed as a general skill remains candidate with outcome provenance/exposure and is supplied to zero unrelated inquiries as an authoritative unscoped rule. | T011 |
| SC-010 | An entrypoint supplied without recipe execution records supplied-only use; a separate fixture records actual execution. | T006, T011 |

## Edge-case evidence map

| Edge case | Planned observable evidence | Task |
| --- | --- | --- |
| EC-001: Active skill becomes stale after source-unit/access-role change. | Only the bound affected version transitions to `stale`; compatible unaffected scope and old inquiry use remain visible. | T009, T010 |
| EC-002: Skill includes a prior evaluation outcome. | Provenance and exposure are retained; an unrelated inquiry receives no unscoped outcome instruction. | T005, T011 |
| EC-003: User explicitly approves a concrete rule and is configured owner. | Approval reference is consumed once; independent checks still gate activation; duplicate prompts are zero. | T007, T008, T011 |
| EC-004: Later worker sees entrypoint but does not execute example. | Supplied reference is recorded; executed-example list remains empty. | T006, T011 |
| EC-005: Skill requires privileges later worker lacks. | Access/scope filter excludes the guidance before context; no skill grants additional privilege. | T004, T005, T011, T015 |

## Frozen interface coverage

| Interface owner | 022 obligation and planned evidence | Task |
| --- | --- | --- |
| 003 extension registry | Export `analyticsExtensionV1` with `extensionId: skill-activation`, dependency on `skill-capture`, activation/review/revoke commands, and scoped `orient`; do not edit the shared registry/controller. | T006, T015 |
| 009 project memory | Publish immutable candidate decisions, activation/lifecycle/use records through `commitProjectMemory`; preserve expected-revision, idempotency, and recovery behavior without a second store. | T006, T014, T015 |
| 014 source discovery | Consume an exact committed source-candidate reference and retain each assertion's declared/observed/tested/inferred provenance and source version. | T013, T014, T015 |
| 015 source compatibility | Reuse its one normative predicate for source eligibility/orientation; do not introduce a second source matcher or substitute seeded active fixtures for activation. | T005, T014, T015 |
| 020 answer memory / 021 skill capture | Preserve single-study results as separate outcome references/exposure and consume committed method/rule candidates without allowing capture to activate them. | T006, T011 |
| 004 live source reuse | Treat 004 as downstream: after 022 commits a real active source version, 004 consumes this provider for its live first/second source inquiries. The 022 candidate-to-active acceptance path does not require 004. | T015, T012 |

## Proposal acceptance cases

The local fixture set retains proposal cases T32-T35, T38-T40, T42, and T59
where they map to the accepted 022 scope: trigger evidence, invalid method,
conflicting definition, prior-outcome exposure, deduplication, access
filtering, answer card versus reusable procedure, later related inquiry, and
single owner approval. Source activation additionally preserves the frozen
014 assertion provenance and the 015 compatibility contract. Proposal `T`
cases remain distinct from feature-local task IDs.

## Result recording template for implementation handoff

| Command / evidence | Result | Linked fixture/test output |
| --- | --- | --- |
| `pnpm test` | Not run at planning handoff | To be completed by T012 |
| FR/SC/edge assertions | Planned, not run | To be completed by T012 |
