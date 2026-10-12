# Implementation Plan: Bounded Worker Admission, Resources and Honest Stopping

**Branch**: 012-bound-worker-inquiry | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from specs/012-bound-worker-inquiry/spec.md

## Summary

Implement the bounded inquiry controller facades in TypeScript. Typed worker proposals are checked against the question, exact dependencies and output declarations, the frozen policy, the graph revision, the remaining aggregate reservation and prior analytical identity. A valid admission is durably recorded through feature 009 before dispatch. The controller distinguishes classified infrastructure attempts from analytical outcomes, reserves required checks and final reporting, applies the canonical inquiry outcome precedence, and reports a scheduling cutoff separately from tested active-process cancellation. Feature 012 owns the only analytical identity predicate; feature 023 will import it.

## Technical Context

**Language/Version**: TypeScript 5.6 or the repository-pinned compatible version; Node.js 20 or later.

**Primary Dependencies**: Existing Node built-ins, feature 008 plan and exposure records, feature 009 stage and append-only evidence interfaces, feature 010 frozen check-policy interfaces, and the neutral execution adapter from feature 001. No new package or runner is introduced.

**Storage**: Feature 009 run-scoped append-only evidence/event stream for inquiry and proposal-admission decisions. Feature 012 supplies typed domain events and consumes the existing commit/recovery services; it does not add a store or project-ledger implementation.

**Testing**: TypeScript build and Node's built-in test runner through pnpm test. Feature acceptance fixtures use synthetic/local inputs and injected persistence, resource and process-cancellation adapters.

**Target Platform**: Local Node.js analytics controller and the synthetic first-fixture profile.

**Project Type**: Internal analytics controller library consumed by the feature 003 inquiry controller.

**Performance Goals**: Admission checks are deterministic and bounded by the finite proposal, dependency and identity records supplied to them. TypeScript does not compute analytical statistics.

**Constraints**: The fixture profile admits at most 900,000 ms of work, 12 worker invocations, four optional branches, three Plan revisions and 300,000 ms per command. Required check and final-reporting capacity is reserved before optional work. Token, monetary, memory and source-byte usage is estimated or unavailable unless an adapter supplies an enforceable measurement. Hard resource requests are refused when the host cannot enforce them. The 15-minute deadline is labeled a scheduling cutoff unless the host's active-process cancellation test passes.

**Scale/Scope**: Budget and resource state is isolated by inquiry ID. All nested work within an inquiry shares that inquiry's aggregate reservation; task concurrency remains the neutral executor's responsibility.

## Product and Trust Boundaries

- The worker proposes purpose, dependencies, expected output, declared resource demand and estimated cost. Its relevance and answer-usefulness judgments retain the worker's reason and cited evidence; deterministic validation does not certify semantics.
- The controller owns grants, policy, identity comparison, graph validity, reservations, admission state and the terminal inquiry outcome. It records each proposal decision before returning it to the dispatcher.
- The feature 003 controller consumes admitProposal and the inquiry outcome resolver. Feature 009 owns durable commit/recovery and stage execution. Feature 010 supplies required checks. Feature 011 and later feature 020 provide the user-facing trace and ledger consumers; this feature emits stable, inspectable record references rather than adding a competing viewer.
- Synthetic acceptance fixtures inject a commit failure, negative result, absent worker prose, nested demand, unknown identity, and both cancellable and scheduling-cutoff host profiles. No external data, service, or model is required by the feature acceptance test.

## Constitution Check

| Gate | Result | Design evidence |
| --- | --- | --- |
| Honest analytical outcomes; no favorable-result retries | Pass | A negative or inconclusive result is not a process failure. Only a separately classified, idempotent infrastructure attempt may be proposed, with a new Execution record and the same frozen analytical inputs. |
| History and evidence remain attributable | Pass | Proposal admissions, rejected/unresolved reasons, reservations, worker/reviewer judgments and infrastructure failures are append-only references; retries and repairs do not rewrite prior attempts. |
| Bounded and accurately advertised guarantees | Pass | Canonical count/time limits are explicit; hard cancellation/resource claims are conditional on adapter tests; unknown cost telemetry is never rendered as zero or exact spend. |
| Single domain writer and recoverable records | Pass | Only the controller admits work. Admission events use feature 009's append-only/CAS mechanism, and dispatch waits for committed or replayed admission evidence. |
| Scoped identity and honest semantic judgments | Pass | The controller compares exact known identity dimensions through the one feature 012 predicate. Unknown dimensions stay unresolved. Semantic assessments keep proposer/reviewer attribution. |
| End-to-end consumer and failure path | Pass | Feature 003 consumes the admission API; committed decision references flow to the inquiry trace. Acceptance fixtures inject commit, cancellation, missing-prose, nested-resource and negative-result failures. |

No constitution exception is required. The design preserves proposal I04/I08/I10/I12/I13/I20 and the accepted feature thresholds.

## Phase 0: Research Decisions

See [research.md](research.md). The accepted graph and interface freeze resolve all technical unknowns. No open clarification is carried into implementation.

## Phase 1: Data and API Design

- [data-model.md](data-model.md) describes the inquiry snapshot, proposal, admission record, shared analytical identity, allowance, execution attempt and terminal answer proposal.
- [contracts/admission-and-stopping.md](contracts/admission-and-stopping.md) defines the feature-local TypeScript seam and the event ordering contract without changing shared interfaces.
- [quickstart.md](quickstart.md) gives the local acceptance walkthrough.
- [tasks.md](tasks.md) contains the executable checklist and [execution.json](execution.json) assigns every task to one implementation unit.

### Implementation Structure

- src/analytics/analytical-identity.ts: AttemptIdentityV1 and the single compareAnalyticalIdentityV1(left, right) predicate. Feature 023 imports it.
- src/analytics/budget.ts: fixture defaults, allowance accounting, resource reservations, reserve policy and observed/estimated/unavailable telemetry labels.
- src/analytics/inquiry.ts: typed proposal validation/admission, context packet, durable-decision-before-dispatch gate, classified execution attempts, inquiry outcome precedence and partial-status rendering.
- src/analytics/acceptance/012.test.ts: synthetic acceptance fixtures for all feature FRs, SCs and edge cases.
- specs/012-bound-worker-inquiry/verification.md: final implementation evidence, written by the last execution unit after its full acceptance run.

### Compatibility

Preserve the frozen admitProposal(inquiryId, proposal) outcome vocabulary and fields in docs/build/interfaces.md. Its runtime dependencies are supplied by the existing controller context; no caller supplies authority, grants, policy or trusted resource telemetry through worker-authored proposal data. Do not edit docs/build/ARCHITECTURE.md, docs/build/interfaces.md, shared controller/runner code or package manifests.

## Post-Design Constitution Check

Pass. Required check/finalization capacity is excluded from optional admission; missing final prose produces an evidence-only status response without another worker call or an invented claim. A valid answer takes precedence over a later process failure. Otherwise the controller applies the canonical order: needs_input, budget_exhausted, execution_blocked, then insufficient_evidence. Process exit status never selects an inquiry outcome.

## Risks and Limits

- The host may support a scheduling cutoff without terminating already-running work. The result must carry that weaker label until the active-process cancellation probe succeeds.
- Local reservations are not OS-level CPU, memory, or source-byte enforcement. Proposals requiring an unavailable hard cap are refused; optional work that can accept a weaker profile carries that disclosure.
- A model's semantic relevance or usefulness judgment cannot be mechanically proved. Missing reasons/citations or a disputed judgment remain unresolved or attributed to a named reviewer.
- Persist-before-dispatch prevents unrecorded admissions but does not create a transaction across the 009 run and project scopes. Feature 003 owns finalization ordering and completion acknowledgement.
