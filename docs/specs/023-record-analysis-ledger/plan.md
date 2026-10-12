# Implementation Plan: Durable Analysis-Attempt Ledger and Repeat Detection

**Branch**: 023-record-analysis-ledger  
**Date**: 2026-09-28  
**Spec**: [spec.md](spec.md)

## Summary

Implement one durable, readable ledger entry for each admitted comparison purpose under a Plan revision. The ledger is an append-only event projection over committed Question, Plan, Execution, Result, Check Result, Claim, and project-memory records. The feature-owned provider in src/analytics/ledger.ts uses the 003 extension hooks and 009 project-memory commit/recovery APIs. It records admission, execution, missing interpretation, rationale, and finalization facts with stable references and replay-safe event identities. Infrastructure retries remain executions in the admitted attempt; an authorized replication is a new linked purpose instance and does not require changing the Plan.

For repeat detection, import compareAnalyticalIdentityV1 from 012 and wrap its dimension comparison with the attributed semantic question judgment required by the feature contract. Return exact, related, incompatible, or unresolved assessments to orientation before 020 performs answer-card ranking. Keep ledger history access-filtered, record permitted retrieval exposure, and recompute current availability/reuse eligibility after grant revocation or source redaction. The ledger show command exposes the question-level timeline; if worker prose is absent, finalization derives a row only from committed controller facts and marks interpretation missing.

## Technical Context

| Area | Decision |
| --- | --- |
| Language and runtime | TypeScript 5.6, Node.js 20 or newer, existing project toolchain |
| Dependencies | Existing 003 extension types, 009 project-memory APIs, 012 analytical identity predicate, and 019 catalog/search surface; no package changes |
| Persistence | Project-scoped memory under .dag-analytics through commitProjectMemory and recoverProjectMemory; immutable event envelopes and record refs are authoritative |
| Projection and search | The current ledger projection is rebuilt from committed domain events; 019 owns the rebuildable SQLite/FTS index |
| Tests | Feature fixtures in src/analytics/acceptance/023.test.ts; pnpm build and pnpm test |
| Runtime surface | 003 lifecycle and CLI extension seam; command path dag analytics ledger show --inquiry <id> |
| Trust boundary | Principal and current grants are resolved by 003/001; 019 filters catalog candidates; 023 never broadens grants or exposes protected paths |
| Performance and scale | No new numerical target is introduced. Work stays within existing inquiry and project-memory budgets and uses bounded orientation output. |
| State constraints | Use canonical ledger match values and existing inquiry/check/evidence states. Unknown required identity remains unresolved; a process exit never establishes analytical support. |

### Production path and ownership

- Producer: 003 calls the 023 provider at admission, after each execution, orientation, and before finalization.
- Durable consumer: 009 commits and recovers project-memory records using the existing CAS and idempotency rules.
- Index consumer: 019 rebuilds/searches permitted ledger summaries from committed records and durable rationale; it remains the index owner.
- Orientation consumer: 020 receives the prior-attempt assessment once before ranking answer cards. 023 does not rank answer cards or put prior outcomes into worker context.
- User inspection: ledger show --inquiry <id> returns the permitted question timeline and references. The 020 answer path may display the same attempt history.
- Failure fixtures: worker crash or absent prose, null and failed work, exact repeat, new window/cohort, replay/rebuild, unknown identity, revoked grant, and redacted source.
- Compatibility: no dag.run.json changes, no second runner or project store, and no edits to 003 controller/CLI, 009 publication, 012 identity, or 019 catalog files.

## Constitution Check

| Principle or gate | Design response | Result |
| --- | --- | --- |
| Honest answer; execution differs from evidence | Preserve null, failed, inconclusive, stopped, and incomplete work as history. Do not map execution success to support. | Pass |
| History is append-only and exposure remains attributable | Append later facts with linked record/event refs; retain actor, reason, Plan revision, and access/exposure refs. | Pass |
| Project memory is scoped and tested | Use 009 project commits/recovery and 019's rebuildable index, preserving stable IDs and access scope. | Pass |
| Bounded autonomy and accurate guarantees | Reuse existing inquiry budgets and local workflow access guarantees; claim no stronger filesystem isolation. | Pass |
| End-to-end producer, consumer, user path, and failure | 003 producer hooks, 020/019 consumers, ledger show timeline, and synthetic crash/repeat fixtures. | Pass |
| Shared architecture ownership | Add only the feature-owned ledger provider and its acceptance test/report. | Pass |

No constitution exceptions or complexity violations are required.

## Design Decisions and Boundaries

1. Treat each admission of a comparison purpose under a Plan revision as one attempt. Store several candidate executions, results, checks, and retries under that attempt. Link a distinct purpose instance for authorized replication even when its Plan and identity are unchanged.
2. Use the 009 project-memory commit and recovery primitives. Commit event payload records with stable event and commit IDs; only records reachable from the committed manifest appear in the ledger. Reconcile the readable projection from the committed chain. Never make the search index authoritative.
3. Reuse the 012 analytical identity predicate without a second field comparator. The ledger owns semantic question-relevance attribution and maps the predicate result to the four canonical match classes.
4. Treat unknown required identity dimensions as unresolved. Exact requires all required dimensions known and equal. Related requires the scoped question to match with an allowed known scope change. A known conflicting target, denominator meaning, or source semantics is incompatible. Similar wording alone is insufficient.
5. Record an explicit reuse or rerun decision before a matching analysis proceeds. A stale, failed, null, or inconclusive result remains findable but is not reusable as supported evidence. Intentional replication creates a linked attempt, not an infrastructure retry and not a Plan edit.
6. Apply current access before returning ledger metadata or evidence refs. The orientation hook returns bounded match assessments only; 020 records any answer-card outcome exposure into worker context. Preserve safe historical pointers after redaction or revocation and recalculate current reuse eligibility.

## Project Structure

### Planning artifacts

- specs/023-record-analysis-ledger/plan.md
- specs/023-record-analysis-ledger/research.md
- specs/023-record-analysis-ledger/data-model.md
- specs/023-record-analysis-ledger/contracts/ledger-extension.md
- specs/023-record-analysis-ledger/quickstart.md
- specs/023-record-analysis-ledger/tasks.md
- specs/023-record-analysis-ledger/execution.json
- specs/023-record-analysis-ledger/verification.md (written by the final implementation unit from actual checks)

### Feature implementation outputs

- src/analytics/ledger.ts
- src/analytics/acceptance/023.test.ts

All other source paths remain owned by their graph nodes. The implementation is a TypeScript feature facade, not a change to the shared CLI/controller or project-memory machinery.

## Post-Design Constitution Check

The design retains analytical and execution facts separately, routes every durable write through 009, routes permission and lifecycle behavior through existing owners, and keeps the ledger projection rebuildable. It provides a producer, orientation consumer, user-readable command, and injected failures. No unresolved technical decision or constitution exception remains.
