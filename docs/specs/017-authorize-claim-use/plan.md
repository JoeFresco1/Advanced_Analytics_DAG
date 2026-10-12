# Implementation Plan: Claim Use Authorization and Lightweight Decisions

**Branch**: `017-authorize-claim-use` | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

## Summary

Implement a policy-owned, versioned use assessment for an exact claim and requested scope, with attributable reviews, canonical authorization transitions, and a lightweight Decision pointer. `authorization.ts` exports `analyticsExtensionV1` through the fixed 003 registry, consumes 010's frozen check eligibility and exact policy refs, and writes immutable use/review/Decision records through 009. A review gate presents exact evidence refs; trace display is recorded separately and cannot authorize use. Descriptive internal uncertainty remains answerable, while consequential decisions and actual external publication require a current configured authorization. `decisions.ts` records a selected action and references its exact claim and assessment without changing claim state or plan exposure.

## Technical Context

**Language/Version**: TypeScript, Node.js 20+; strict project TypeScript configuration.

**Primary Dependencies**: 003's fixed extension registry, principal/context services, lifecycle hooks and answer/trace baseline; 010's frozen evidence policy and required-check eligibility; 016's canonical current-validity and dependency invalidation transitions; 009's `commitProjectMemory` and recovery/CAS persistence. No new package or shared-interface change.

**Storage**: Immutable policy, use-assessment, trace-seen, review and Decision records in the existing 009 project sidecar. Stable record IDs, revisions and digests are the identity; 009 remains the sole writer. No state is written to `dag.run.json`.

**Testing**: Synthetic/local Node test fixture `src/analytics/acceptance/017.test.ts`, run with the installed `tsx` loader. Final verification also runs `pnpm run build` and `pnpm test`.

**Target Platform**: Local analytics CLI/controller and existing answer/trace record path. Demonstrations use synthetic records only; no runner is invoked by the acceptance fixture.

**Project Type**: TypeScript analytics controller provider and record facade under `src/analytics/`.

**Performance Goals**: Bounded evaluation over the finite exact reference set for one Claim and one requested use. No new latency or throughput threshold is introduced.

**Constraints**: Preserve the accepted 017 spec, checklist, policy thresholds, reference answers and frozen interfaces. Do not edit shared registry/controller, 010 policy files, 016 recurrence files, 009 persistence, viewer, package manifests, or other feature files. Use canonical authorization and Claim states; retain history; no process status, trace opening, risk acceptance, or reviewer label may be represented as evidence support. No software repair/retry semantics are introduced.

**Scale/Scope**: One immutable use assessment per requested use/evidence binding, append-only review and invalidation events, and one lightweight pointer per selected action. The authority route and trigger rules are configured and versioned by the project owner; workers cannot create or weaken them.

## Constitution Check

### Before design

- **Honest answers and uncertainty (Constitution I/V; proposal I09)**: Pass. Internal inconclusive or conflicting conclusions may finish with limitations. The consequential action/publication path is separately gated and does not force an uncertainty-only inquiry to fail.
- **Execution, evidence, review and authorization remain distinct (Constitution II/III; proposal I03/I05/I06/I09)**: Pass. Claim state, exact checks, Plan exposure, review provenance, use authorization and Decision remain separate records and dimensions. Risk acceptance never changes a Claim or exposure label.
- **Policy and authority are owner-controlled (access and judgment contract)**: Pass. A 017 use-policy record is immutable and owner-registered; it references the exact 010 evidence policy. The worker cannot select an approving authority or amend either policy.
- **Append-only and exact evidence (proposal I12; architecture)**: Pass. Refs bind record ID, revision and digest. Staleness/revocation is appended through 009; recovery and replay use its stable commit IDs and CAS boundary.
- **End-to-end feature path (Constitution VI; proposal I14)**: Pass with the scoped consumer path. The 017 provider registers through 003, consumes 010/016 policy state, returns inspectable committed refs to existing answer/trace commands, and is exercised with synthetic approval, denial, stale, revoke and persistence-failure cases.
- **Shared ownership and local guarantee**: Pass. Only graph-owned 017 files are produced; the plan does not claim strong isolation or edit locked run files.

No constitution exception or policy-threshold change is required.

## Design

### Policy ownership and use triggers

`authorization.ts` defines the 017-owned `UseAuthorizationPolicyV1` record. A configured project owner registers an immutable policy revision through the owner-authenticated `use policy register` extension command. The record declares effective Claim/source/scope/audience, use trigger rules, authority route, required review provenance, rationale requirements, permitted risk-acceptance conditions, and a link to the exact 010 evidence-policy revision. This keeps use-trigger ownership explicit without extending the frozen 010 or shared interfaces. A worker may submit a use request but cannot create, change, or activate the policy.

Policy triggers are evaluated from recorded intent and use events, not from a mode label alone. They include a consequential decision link, a real external-publication request, and a material audience/use/scope change. Saving an internal report alone does not trigger external-publication authorization. The policy records whether a request is routine internal, consequential decision, or external publication and the exact scope. A consequential or external-publication trigger requires a configured authority route; a missing route leaves the request unresolved and blocks that use. A changed intended use creates a new assessment; it never inherits approval from a broader or earlier scope. Off-platform copying remains outside observable use events.

### Exact evidence binding and canonical transitions

A `UseAssessmentV1` pins the exact Claim revision; all supporting and challenging Result revisions; applicable Check Result revisions and 010 eligibility; linked Review revisions; locked Plan revision and exposure label; 010 evidence-policy ref; 017 use-policy ref; current source/artifact refs required by the claim; requested use, audience and scope; configured authority ref; and predecessor assessment ref when reassessed. Missing required evidence is recorded as missing and cannot qualify an assessment. The controller checks identities, digests, scope, policy versions and state values; it does not claim to prove the semantic meaning of prose.

Before any use request, the displayed state is `not_assessed`. A request with a required but incomplete review/evidence decision remains `pending_review`; a trace presentation alone has no transition effect. For consequential or external-publication use, `authorized` requires current exact evidence eligible under 010 and an affirmative decision from the configured authority. Routine internal use may remain outside the gate when its configured policy permits that. A complete explicit negative authority decision becomes `denied`. A policy-permitted exception becomes `risk_accepted` only when the named authority records the exact use, accepted uncertainty and substantive rationale. `stale` is the current-use overlay after any material bound change; authority withdrawal produces `revoked`. A re-evaluation creates a new linked assessment and never edits the historic authorization.

The `authorization.ts` facade consumes 016 invalidation/current-validity outputs. Any material revision of Claim meaning, Result, Check, Plan/exposure, source/artifact, evidence policy, use policy, audience, requested use or scope marks affected assessments stale before new judgment. New current, methodologically comparable Results pointing materially in opposing directions require an attributed `conflicting` Claim reassessment with both Result refs and a reason; different populations or estimands remain separate Claims. Ordinary `authorized` cannot cover conflicting or negative evidence. Where the configured policy allows proceeding, a separate use-scoped `risk_accepted` assessment records the uncertainty without relabeling the Claim.

### Review provenance and trace presentation

A review record pins the assessment and evidence refs it considered, actor principal, actor category (`worker`, `separate_agent`, or `human`), configured role/authority match, rationale, unresolved issues, time and outcome. Actor categories are never inferred from the number of model calls. A separate-agent review is distinct from worker self-review, but it is procedural review provenance and does not establish independent empirical evidence. Only a configured authority can issue consequential-use authorization, risk acceptance or denial. A routine internal assessment may proceed without a human gate when its policy permits.

The use-assessment command returns the trace refs and exact current binding to the caller for presentation. A `trace_seen` event records that the refs were presented or explicitly opened, but cannot by itself satisfy a review, rationale or authority gate. An empty or non-substantive rationale, worker self-review where a distinct review is required, or a reviewer who lacks the configured authority leaves the use `pending_review`. The command response and committed refs are inspectable through the existing 003 answer/trace path; this feature does not edit the viewer.

### Decision pointer and persistence

`decisions.ts` creates a small immutable `DecisionV1` with selected action, deciding principal, exact Claim refs, the exact current `UseAssessment` ref, constraints/rationale, decision time and external action reference. Required fields are validated before commit. It is a pointer to what a person chose to do; it neither executes the action nor upgrades the Claim. A Decision can be finalized only against a current `authorized` or policy-permitted `risk_accepted` assessment for that exact use. A later correction or revocation preserves the Decision and marks its current use relationship for reassessment through the existing 016 invalidation path.

The provider exports `analyticsExtensionV1` with `extensionId: use-authorization` and `dependsOn: [recurrence]`. Its commands register owner-authored use policy, request and review use, expose assessment status/trace refs, and record Decisions. Its `beforeFinalize` hook checks any policy-declared consequential use and prevents that use from proceeding without current authorization. An honest internal uncertain answer may still complete; pending or denied authorization blocks the consequential action/publication, not the factual answer. Assessment, review and Decision writes go through 009 `commitProjectMemory`; the fixture injects a failed acknowledgement and verifies recovery/replay cannot create duplicate approval or make a partial record visible.

### Project Structure

- `specs/017-authorize-claim-use/plan.md`, `research.md`, `data-model.md`, `contracts/use-authorization-v1.md`, `quickstart.md`, `tasks.md`, `execution.json`, and `verification.md` are feature-scoped planning/verification artifacts.
- `src/analytics/authorization.ts` owns policy triggers, use assessments, review provenance, state transitions, commands and lifecycle provider.
- `src/analytics/decisions.ts` owns immutable Decision pointer validation and construction.
- `src/analytics/acceptance/017.test.ts` owns synthetic gate, history, registry, persistence-failure and finalization fixtures.

**Structure Decision**: Keep behavior in the graph-owned analytics files and use the frozen 003 extension seam and 009 persistence service. 010 remains the owner of evidence-check policy; 016 remains the owner of recurrence invalidation; 017 owns intended-use authorization. `verification.md` records implementation results only after the final task runs the declared checks.

## Delivery and Verification

Feature prerequisites are 003 (registry, actor/context and answer/trace services), 016 (current-validity/dependency invalidation), and 010 (frozen required checks and presentation eligibility). 009 is consumed as the existing durable project writer. The planned units are: policy and review gate; use changes and Decision integration; final acceptance and evidence report. The focused fixture covers every 017 FR, SC and ordered edge case, including the worker-versus-separate-agent review attribution and configured-owner authority gate. Proposal cases T16, T21 and T23 are exercised without turning them into feature task IDs.

The final acceptance unit runs the focused fixture, `pnpm run build`, and `pnpm test`, then records actual outcomes and evidence links in `verification.md`. Planning-stage artifacts state that these commands have not yet run. No product runner, external service, real source, or reference answer is used.

## Post-Design Constitution Check

Pass. The design uses the canonical authorization values without adding a new authorization state. It preserves Claim evidence and Plan exposure through risk acceptance; binds the current use to exact Claim/Result/Check/Review/Plan/evidence-policy/use-policy/scope refs; creates new linked assessments on re-evaluation; treats trace viewing as exposure/audit metadata only; and keeps Decision separate. Internal uncertainty can be returned honestly while consequential action requires current authority. All persisted domain records go through 009, and the provider registers through 003 without editing shared files.

## Complexity Tracking

No constitution violations or new architectural projects are introduced.