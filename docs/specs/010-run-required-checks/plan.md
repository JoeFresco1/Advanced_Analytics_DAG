# Implementation Plan: Frozen Policies and Structured Required Checks

**Branch**: `010-run-required-checks` | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from `specs/010-run-required-checks/spec.md`

## Summary

Implement owner-registered, immutable check-policy versions and policy-owned executable validators. Admit only validator parameters permitted by the registered profile, execute through the existing 009 stage coordinator, and persist the frozen `CheckResultV1` contract with exact Plan, policy, validator, and subject bindings. Keep process execution status separate from the analytical check outcome. Required-check coverage detects missing and stale results, and a deterministic eligibility function applies the full supported-presentation truth table. A successful validator process that finds a fault records `fail`; an unfavorable analytical result never triggers retries intended to obtain a favorable outcome.

## Technical Context

**Language/Version**: TypeScript on Node.js 20 or newer, per `package.json`.

**Primary Dependencies**: Existing TypeScript 5.6 toolchain, Node test runner, and `tsx` development dependency. Do not add dependencies: shared package and schema dependency changes are owned by build node 001.

**Storage**: Versioned analytics sidecar records committed through the 009 publication service. `CheckResultV1` is immutable evidence and references stable record revisions and SHA-256 digests; no analytics domain state is written to `dag.run.json`.

**Testing**: Feature fixtures in `src/analytics/acceptance/010.test.ts`, run through `tsx --test`; repository acceptance uses `pnpm build` and `pnpm test`.

**Target Platform**: Local TypeScript analytics controller and synthetic/local fixtures. Any local-shell access limit is described as a workflow boundary unless a host/source permission boundary enforces it.

**Project Type**: TypeScript CLI/controller with a local evidence sidecar; feature 010 supplies the policy and check producer/consumer contract for later trace rendering.

**Performance Goals**: Execute the finite validators admitted by a frozen policy within their declared stage timeout. No new throughput or latency threshold is introduced by this feature.

**Constraints**: Policy and validator versions are project-owner controlled. Unknown fields, unsupported profiles, missing required checks, mismatched subjects, stale policy/validator revisions, and unjustified `not_applicable` outcomes fail closed. Preserve check evidence even when it blocks support. Reuse `executeStageV1` and 009 commit/recovery; do not create a runner, alter shared interfaces, change policy thresholds, or add a worker-controlled executable path.

**Scale/Scope**: One versioned policy profile per declared method/scope and a bounded required-check set per Plan. No cross-project policy authority or general policy editor is introduced.

## Constitution Check

| Principle / invariant | Plan control | Gate |
| --- | --- | --- |
| Constitution II; proposal I03/I04 | Bind each Check Result to exact subject, policy, validator/code, Plan, and execution revisions. Treat command completion separately from `outcome`; keep valid negative results without favorable-result retries. | Pass |
| Constitution III; proposal I05/I06 | Only the configured project owner may register a new immutable policy version. A worker proposal cannot publish, remove, or relax required checks. A policy change creates a new reference and re-evaluation; history is retained. | Pass |
| Constitution V; proposal I08/I13 | Missing evidence stays missing; stale evidence stays stale. Describe local authority as controller-enforced workflow policy, not protection from an administrator who can rewrite the controller. | Pass |
| Constitution VI; proposal I14 | `checks.ts` produces records, the eligibility evaluator consumes them, and feature 011 plus integration 002 expose the trace. Feature 010's acceptance path covers the executable producer, controller decision, and seeded failure; it does not claim the downstream browser view is owned here. | Pass with stated boundary |
| Shared writer and recovery; proposal I12 | Publish records through the existing 009 stage and append-only commit interfaces; do not bypass its writer or recovery boundary. | Pass |
| Synthetic/local demonstration requirement | All fixtures and examples use synthetic records and local execution. No confidential or remote source is required. | Pass |

No constitution exception or policy-threshold change is required. The final design retains negative, inconclusive, error, missing, stale, and unsupported not-applicable evidence as distinct facts.

## Design

### Policy and trust boundary

`src/analytics/policies.ts` owns the strict policy profile schema, configured-owner registration, immutable policy references, method/scope applicability, the required validator set, validator version/code references, parameter constraints, permitted `not_applicable` reasons, claim-presentation rule, and authority route. Registration validates the profile and executes its policy-owned fixture set before the profile can be used for a dependent execution. A worker can propose a profile or extra check, but only the configured owner route can register a new version. A proposed profile is not effective policy. Updating any policy creates a new record/version; it never edits a historical failure or silently reuses prior checks.

Worker-supplied check parameters are admitted against the registered validator descriptor before execution. The worker cannot supply or replace the executable, validator ID/version, required-condition list, allowed N/A reasons, authority route, or presentation rule. Reject unknown parameters and values outside the frozen parameter contract. Bind the admitted canonical parameter digest into the execution/check evidence.

The local authority check is controller-enforced. It records the approving configured owner and does not claim to prevent a privileged local process from modifying the repository or controller.

### Validator execution and Check Results

`src/analytics/checks.ts` resolves every required validator from the frozen policy registry and invokes it through 009's `executeStageV1` (`src/analytics/stages.ts`). It passes an explicit executable/argument vector and declared result schema; it does not interpolate untrusted values into shell text or accept a worker-selected command. It uses the execution service's zero generic retry configuration. Classified infrastructure recovery remains the 009 execution concern and is recorded separately; a check outcome of `fail`, `inconclusive`, `error`, or a valid negative observation does not schedule an analytical retry.

Persist the interface-frozen `CheckResultV1`: `schemaVersion`, `checkId`, `executionId`, `planRef`, `policyRef`, validator ID/version/code reference, exact subject references, canonical `outcome`, observed and expected values, evidence references, tested failure classes and limits, and reason. Record process status in its execution record. A zero process exit can accompany `outcome: fail`; it means the validator ran and found the declared condition. A failed process, malformed/missing output envelope, or output that fails exact-binding/schema checks becomes an evidence/check `error`, not a pass. Keep raw malformed output as diagnostic evidence when available.

### Exact subjects, freshness, coverage, and presentation

Subject references include stable identity, revision, digest, and artifact identity where applicable. Match results only when Plan, policy, validator ID/version/code, and every exact subject match the current required condition. A changed subject or policy never inherits an earlier pass. Missing and stale are coverage findings, not `CheckResultV1.outcome` values; preserve the original result in history and report current coverage separately.

For each dependent Claim, the controller evaluates each condition from the frozen policy. Current `pass` satisfies that condition. Current `not_applicable` satisfies it only when the reason is explicitly allowed by that policy version; it remains `not_applicable`. `fail`, `error`, `inconclusive`, missing coverage, stale coverage, unsupported method/policy scope, and unjustified `not_applicable` block the Claim. All required conditions must be satisfied for `supported_presentation_eligible: true`. Risk acceptance and review cannot convert any check into pass or change Claim evidence state.

The eligibility result is a deterministic controller assessment over the exact Claim, Result, Check Result, artifact, validator, and policy revisions. It does not assert semantic validity beyond the declared checks and their failure-class limits.

### Integration and ownership

Feature 010 owns only the graph-listed outputs: this plan, `tasks.md`, implementation verification report, `src/analytics/checks.ts`, `src/analytics/policies.ts`, and `src/analytics/acceptance/010.test.ts`. It imports 009's stage/publication services and consumes the shared Check Result and reference contract in `docs/build/interfaces.md`. It does not change `docs/build/ARCHITECTURE.md`, `interfaces.md`, fixture lock/reference answers, shared runner/tooling, accepted spec/checklist, package manifests, or another feature's files. Feature 011 consumes these records for the browser evidence trace; integration feature 002 demonstrates the full fixed-analysis path.

## Project Structure

```text
specs/010-run-required-checks/
├── plan.md
├── research.md
├── data-model.md
├── contracts/required-checks-v1.md
├── quickstart.md
├── tasks.md
└── execution.json

src/analytics/
├── checks.ts
├── policies.ts
├── stages.ts                         # owned by 009; imported, not edited
└── acceptance/010.test.ts
```

**Structure Decision**: Use the existing `src/analytics/` TypeScript controller boundary and one feature acceptance file. Policy identity and approval live in `policies.ts`; check execution, structured records, coverage, and presentation eligibility live in `checks.ts`. There is no new service, runner, package, or shared schema implementation.

## Delivery Units and Verification

The local task/unit order is policy contract → executable check path → coverage and presentation evaluation → final acceptance report. The feature depends on build node 009 for execution and durable publication. The final acceptance unit runs `pnpm build` and `pnpm test`, and writes `specs/010-run-required-checks/verification.md` with actual results and FR/SC evidence links.

The fixture matrix covers SC-001 through SC-006 and all three feature Edge Cases. Proposal cases T02, T03, T04, T10, and T23 are required in the final feature fixture path: stale artifact (T02), successful execution with overlap failure (T03), unfavorable baseline without retry (T04), omitted/renamed required validator (T10), and post-check code/policy change invalidation (T23). T02/T04 are included because the accepted feature spec and its edge map own them, alongside the graph's explicit T03/T10/T23 acceptance.

## Constitution Check (Post-Design)

The design preserves the frozen check vocabulary and shared `CheckResultV1`, uses the configured owner boundary, and leaves approved requirements and thresholds unchanged. Every passing fixture records tested failure classes and limits. Failed and inconclusive evidence remains visible. Missing, stale, and unsupported policy conditions block eligibility without being mislabeled as validator outcomes. No unresolved design question blocks task generation.
