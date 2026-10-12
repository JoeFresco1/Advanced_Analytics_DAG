# Recurrence and Use Integration Contract

## Boundary

This feature adds a synthetic production-path demonstration and its acceptance test. It adds no public analytics endpoint, controller lifecycle, persisted schema, CLI vocabulary, policy, or state. The normal analytics CLI/controller is the caller. Existing providers remain authoritative:

| Capability | Provider/owner | 005 behavior |
| --- | --- | --- |
| Source skill identity, compatibility, freshness and supplied-version event | `source-reuse` / 015 | Pin the exact compatible version and surface a stale result when its relevant meaning changes. |
| Window acquisition identity, comparison, repeated-look exposure and correction impact | `recurrence` / 016 | Request transitions through the owner and display its exact returned revisions/references. |
| Claim-use review, authorization, stale/revoked assessment and Decision | `use-authorization` / 017 | Establish a baseline use and Decision before correction; request a distinct reassessment afterward. |
| Commit, recovery and committed record reads | 009 | Resolve stable record references, commit linked revisions and retain replay behavior. |
| Report assertion, answer and evidence trace | 011 | Open the existing browser answer/use trace after current grant checks. |
| Answer-card production/retrieval and ledger production/match | 020 / 023 | Not invoked or implemented by 005. Their references appear as opaque synthetic fixture inputs to 016's dependency traversal and the 011 link view. |

The graph prerequisites are 004, 016, and 017. Viewer 011 is in the prerequisite closure through 004 → 003 → 002 → 011. The integration acceptance must not require 020/023 modules to be installed. It must not suggest their producer behavior is exercised by the opaque-reference fixture.

## Scenario protocol

1. Run the standard `dag analytics ask` path on a local synthetic source with `gpt-6-luna` and `xhigh`; capture the inquiry, exact source/window, Plan, execution, Result, Check, Claim and report assertion references.
2. Recur on a second bounded window using the exact 015-approved skill version. Record event-time and processing-time bounds separately, source position, completeness, lateness and label maturity when relevant. Have 016 return the canonical comparison and its reasons. A same-schema semantic change must not appear as an unqualified trend.
3. Record an inspection and its planned/exposed status under 016 before selecting any favorable result. The answer/trace page displays the look reference and canonical plan interpretation state; it never grants an independent-confirmation label to an undeclared look.
4. Before correcting data, write a report assertion and use path through 017. The Use Assessment binds exact Claim, Result, Check, Review and policy revisions. The Decision binds its action, actor, exact Claim revisions, time, constraints and external action reference.
5. Ingest a late event only through 016, after the report and Decision exist. Commit a new source version and correction relationship, then inspect all nine impact categories from `data-model.md`. Preserve prior report/evidence/Decision. Show stale or reassessment-required state and separate reproduction state; if bytes are unavailable, show `unavailable` without erasing historic metadata.
6. Request 017 reassessment against the new exact evidence and policy revision. The old Use Assessment remains `stale`; the old Decision remains an immutable historical record. A current assessment and any new Decision use new references.
7. Exercise routine internal use with both `inconclusive` and `conflicting` Claim evidence. The inquiry may finish as `answered_with_uncertainty`; evidence remains unchanged. A consequential use with conflicting or negative evidence requires the explicit, policy-permitted `risk_accepted` path.
8. Save/export an internal report without creating an external-publication authorization. Open the review trace with an empty rationale and assert that the assessment remains `pending_review`. Make a later actual publication request through 017 policy evaluation.

## Viewer contract

The existing 011 answer/trace surface is the user inspection path. It must display the answer and scoped limitation first, then permit navigation over committed IDs through the report assertion, Claim, Result, Checks/Review, Plan/exposure, source/window/correction, skill version, Use Assessment, and Decision. The correction impact pane links all nine 016 record categories, including the fixture-supplied answer-card and ledger IDs, with access-safe current validity or reassessment information. Reproduction, evidence availability, Claim evidence, plan exposure and authorization are shown as separate values. Repeated-look exposure is visible from the exact Plan/look references.

The viewer must use stable IDs and current viewer grants. Restricted metadata or bytes remain withheld with a permitted explanation. The feature does not edit `viewer/analytics.html`, `src/analytics/http.ts`, `src/analytics/report.ts`, shared controller files, or extension registry files.

## Acceptance commands

The deterministic fixture test is runnable without external data or services:

```powershell
pnpm exec tsx --test src/analytics/acceptance/005.test.ts
```

The local demonstration entry point accepts only the synthetic fixture and the configured harness:

```powershell
pnpm exec tsx src/analytics/demo-recurrence.ts --fixture recurrence-use-synthetic --model gpt-6-luna --reasoning xhigh
```

The final verification also runs `pnpm build`, `pnpm test`, and `pnpm smoke:viewer`. These commands validate implementation when the feature is built; this planning stage does not run them.
