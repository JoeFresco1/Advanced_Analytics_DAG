# Implementation Plan: Integrated Delegated Inquiry

**Branch**: 003-delegated-inquiry | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from specs/003-delegated-inquiry/spec.md

## Summary

Integrate a production question-to-answer path in the shared analytics CLI and controller. The user submits ordinary question text and a local synthetic fixture through `dag analytics`, registered by 001 in the live `src/commands/index.ts` dispatcher through its lazy trampoline and handled by the `analyticsCmd(argv, cmd)` export in `src/analytics/cli.ts`. The worker receives an access-filtered inquiry context, orients the question, and authors typed plan, code, check, and answer proposals. The controller registers and locks valid plans, durably records exposure through feature 008 before supplying exposed context, admits work through features 008 and 012 between settled stages, executes authoritative Python calculations and frozen checks through feature 009, and returns a scoped answer with a browser trace. The integration also owns the fixed analytics extension registry and replayable completion protocol. A result is not returned as completed until the run terminal intent, required project-memory writes, and run completion acknowledgement are durable.

## Technical Context

**Language/Version**: TypeScript 5.6 and Node.js 20 or later, using the repository-pinned toolchain.

**Primary Dependencies**: Existing Node built-ins, the analytics worker harness adapter, generated Plan and computation schemas from feature 001, data and descriptive facades from 007, Plan/exposure registration from 008, stage execution and durable commit/recovery from 009, frozen required checks from 010, answer/trace viewer from 011, bounded admission from 012, and predictive computation from 013. No package or runner is added.

**Storage**: Feature 009 run-sidecar records and project-memory commits. Feature 003 coordinates the existing APIs and does not add a storage engine or change dag.run.json.

**Testing**: TypeScript build and Node built-in test runner through pnpm build and pnpm test. Feature acceptance uses deterministic synthetic/local fixtures and injected worker, harness, provider, check, and persistence adapters. A separate recorded live-harness walkthrough uses gpt-6-luna with xhigh reasoning.

**Target Platform**: Local Node.js analytics CLI and the synthetic first-fixture profile, with the feature 011 browser answer/trace route.

**Project Type**: Analytics CLI, inquiry controller, worker adapter, and extension registry within the existing TypeScript application.

**Performance Goals**: A bounded request processes finite plan, stage, hook, and evidence records. The worker may propose analysis, but numeric predictive results come from the authoritative Python calculator; TypeScript validates bindings, policy, and presentation eligibility.

**Constraints**: Preserve all accepted spec/checklist text and frozen interfaces. Keep factory semantics out of worker context. Use no untrusted import path or worker-selected privilege. Preserve null and negative outcomes without favorable-result retries. Honor the feature 012 fixture allowance of 15 minutes, 12 worker invocations, four optional branches, three Plan revisions, and five minutes per command; report cost telemetry as unavailable or estimated when appropriate. Label locally readable withheld evaluation as workflow-withheld; access isolation not enforced.

**Scale/Scope**: One complete synthetic tabular predictive inquiry, including signal, null, exact-delta boundary, denominator and leakage faults, revision and restart history, budget stops, report-value validation, extension lifecycle behavior, and browser trace. Other method profiles, source skills, answer ranking, and project-ledger policy remain owned by their focused features and are consumed through the frozen extension seam.

## Constitution Check

| Gate | Result | Design evidence |
| --- | --- | --- |
| Honest question outcome | Pass | The controller selects one canonical inquiry outcome from evidence and the feature 012 precedence rule. Process exit codes cannot select it; null and negative comparisons remain valid answers. |
| Execution is distinct from evidence | Pass | Worker-authored code/checks are candidates. The controller admits exact artifacts between settled stages and calls 009 stage execution, 013 authoritative computation, and 010 frozen checks. Only bound results can support displayed numerical assertions. |
| Historical exposure is immutable | Pass | Each plan revision, execution, exposure event, and worker context digest is retained. Outcome-informed revisions and restarted harnesses inherit prior exposure; approval does not relabel the plan. |
| Access and semantic judgment stay attributed | Pass | Principal and grant resolution precede source, memory, and viewer access. Worker meaning/usefulness judgments retain reasons and evidence references. The controller validates structure, identity, access, budget, and policy without presenting that as proof of semantics. |
| Bounded and accurately described work | Pass | The controller consumes 012 reservations and reserves required checks/reporting. Unsupported cost and filesystem-isolation guarantees remain explicitly unavailable or workflow-level. |
| Single writer and durable finalization | Pass | One controller coordinates each run. Stable IDs replay run terminal intent, project-memory writes, and completion acknowledgement through 009; no completed response is exposed before all required acknowledgements are durable. |
| End-to-end producer, consumer, viewer, and failure | Pass | The worker produces proposals; controller/registry and authoritative stages consume them; the browser shows the scoped answer/trace; synthetic faults exercise registration, hook, measurement, leakage, revision, restart, budget, finalization, and report binding. |

No constitution exception is required. The implementation does not change feature thresholds, reference answers, check policy, the state vocabulary, or shared interfaces.

## Phase 0: Research Decisions

See [research.md](research.md). The accepted feature spec, dependencies, graph, architecture, and interface freeze resolve the design choices. No open clarification remains.

## Phase 1: Data and API Design

- [data-model.md](data-model.md) describes the question, worker invocation, plan/exposure, admitted stage, computation/check, claim/report, extension capability, and finalization records.
- [contracts/delegated-inquiry-v1.md](contracts/delegated-inquiry-v1.md) specifies the frozen command, provider, lifecycle, CLI, and durable-finalization seams used by this integration.
- [quickstart.md](quickstart.md) describes synthetic acceptance and live CLI walkthroughs.
- [tasks.md](tasks.md) and [execution.json](execution.json) assign each implementation task to exactly one bounded unit and map all FR, SC, and ordered edge cases.

## Implementation Structure

~~~text
src/analytics/
├── worker.ts                 # Worker orientation, bounded invocation, typed proposals
├── controller.ts             # Admission, stage orchestration, outcomes, finalization
├── cli.ts                    # dag analytics ask, answer show, and trace dispatch
├── extensions.ts             # Fixed provider allowlist, typed commands, lifecycle hooks
└── acceptance/
    └── 003.test.ts           # Integrated synthetic and fault acceptance fixtures

src/commands/
└── index.ts                  # Register the analytics handler in the live dag dispatcher

specs/003-delegated-inquiry/
└── verification.md           # Written by the final implementation unit from actual checks
~~~

The five `src/analytics/` product-code outputs above remain the graph-assigned 003 files. Feature 001 owns the existing `src/commands/index.ts` dispatcher and registers a lazy `analytics` route during factory cleanup. Feature 003 supplies the `analyticsCmd(argv, cmd)` export in `src/analytics/cli.ts` consumed by that route; it does not edit the registry. This is the production CLI integration point, not a new runner or a redesign of the neutral command registry. Feature 003 does not edit package manifests, runner files, shared architecture/interfaces, the run file, fixture locks, accepted specs, or focused feature facades.

## Compatibility

The 001-owned production `src/commands/index.ts` registry routes the top-level `analytics` token through its lazy trampoline to `analyticsCmd(argv, cmd)` in `src/analytics/cli.ts`; the latter parses the frozen nested actions:

~~~powershell
dag analytics ask --project <path> --question <text> --source <fixture> --model gpt-6-luna --reasoning xhigh
dag analytics answer show --inquiry <id> [--open]
dag analytics trace --claim <id>
~~~

Users provide a question and source; they do not construct YAML or choose graph nodes. The controller reuses 009 executeStageV1 and commit/recovery services, 008 registration/exposure, 012 admission/budgets, 007 lineage, 010 required checks, 013 predictive calculation, and 011 viewer routes. The ordinary orientation path records 008 exposure durably before including exposed prior results in worker context; the ordinary observation path records viewed outcomes before a substantive outcome-informed revision can be proposed. Unknown history remains `exposure_unknown`; early or unplanned analysis remains `exploratory` or becomes `outcome_informed` after outcome exposure and substantive change, as applicable. None can be labeled `planned_confirmatory` without a locked plan and declared inspection rule preceding relevant outcome exposure. It does not create a parallel runner or handwritten duplicate of the Pydantic Plan/computation schemas.

The registry loads only the module/export pairs frozen in docs/build/interfaces.md. Missing exact optional modules become explicit unavailable capabilities; import, export, dependency, schema, duplicate-command, or cycle errors fail closed. Providers cannot change access, policy, or budget. Hooks execute in dependency order, and required hook failures retain history while preventing completion.

## Post-Design Constitution Check

Pass. The design preserves the original question and operational assumptions, separates worker proposals from controller admission and authoritative computation, applies the locked predictive delta rule, shows prediction-versus-causation and negative-result limits on the first answer screen, binds values to committed evidence, and does not acknowledge a completed inquiry before its stable-ID finalization sequence is durable. The boundary predictive fixture tests equality at the locked delta; opaque worker-only values are explicitly excluded from supported report values.

## Risks and Limits

- The local harness may have broader file access than the product surfaces can enforce. The answer labels workflow withholding and does not claim an isolated holdout.
- Model prose cannot prove semantic correctness. Unresolved population, denominator, intent, or causal meaning remains attributed and may require a scoped limitation or user decision.
- A source/report value without an authoritative result binding can remain visible as unsupported diagnostic text only; it cannot be rendered as a supported assertion.
- A process may stop between run and project persistence. Stable IDs and the acknowledgement protocol allow replay; an unacknowledged intent remains visibly incomplete.
- Optional extension absence is expected during incremental graph slices. A present provider with runtime/import faults is never silently treated as absent.
