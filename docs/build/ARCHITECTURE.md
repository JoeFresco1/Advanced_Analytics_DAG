# Shared implementation architecture

**Status:** frozen design for the analytics build graph  
**Date:** 2026-09-28  
**Scope:** shared stack, trust boundaries, record kernel, and graph ownership. This document defines interfaces and implementation ownership; it does not claim that the planned runtime exists.

## Product contract

The proposal revision 2.6, constitution 1.4.1, canonical state vocabularies, access and judgment contract, and all 23 accepted feature specifications govern product meaning. The [build plan](00-plan.md) sets the implementation sequence. The [graph](graph.json) remains the file-level source of feature ownership.

Execution, evidence, claim state, reproduction, review, authorization, and inquiry outcome are separate facts. The product retains null, negative, conflicting, inconclusive, failed, and incomplete work. A process exit or green execution graph cannot establish analytical support. A valid unfavorable result cannot initiate a retry seeking a favorable value.

## Stack and runtime boundaries

| Area | Chosen stack and authority |
| --- | --- |
| Analytics controller, CLI, policy, access, storage adapters, HTTP API | TypeScript, under `src/analytics/`. The controller admits work, binds evidence, applies policy, and finalizes inquiries. |
| User interface | TypeScript server/API with a purpose-built analytics answer and trace page at `viewer/analytics.html`. Feature 011 owns the first viewer path. Existing `viewer/run.html` is migration context. |
| Tabular computation | A uv-locked Python package at `python/`. Pydantic request and result contracts are mode-specific for descriptive, predictive, and inferential work. Python is the authoritative calculator; TypeScript admission, binding, policy, and presentation checks govern whether results can be committed or shown as supported. |
| Execution | One neutral execution package, `@dag-orchestrator/neutral-execution`. It alone schedules admitted task dependencies and owns process launch, logs, locks, timeouts, artifact paths, and interrupted execution recovery. |
| Local project search | SQLite with FTS5 as a rebuildable lexical index. It is a projection over durable manifests and curation events, never the authority for analytical records. |

The worker authors plans, programs, explanations, and bounded proposals. It does not write committed records or select its own access grants, check policy, release threshold, or final inquiry outcome. A worker's semantic judgment keeps its attribution and reason. The controller performs deterministic checks on schemas, references, digests, grants, state vocabularies, graph shape, and budgets; it does not claim to prove that an interpretation is semantically correct.

### Authoritative neutral execution package

Node 001 creates the authoritative source package at `packages/neutral-execution/` in this repository by extracting neutral scheduling/process/storage behavior from the inherited engine. The analytics package consumes that workspace package at its exact declared version. The root runner copy is removed, and all later engine changes belong to this one package. Node 001 also produces a local release archive, its actual content digest, and a tested adapter/conformance fixture for the original software product. Remote publishing is not a build prerequisite. Package artifacts are generated outputs, not separately maintained source copies.

The original software product remains on its frozen legacy engine version during this migration. Its documented adoption path consumes the same local release archive and runs the shared neutral conformance vectors; feature 001 must test that adapter against the legacy execution contract before claiming compatibility. This build does not silently edit the original repository or claim that its dependency has already migrated. The package manifest, workspace lock, artifact digest and conformance report identify the real version produced; no invented registry pin or undocumented second scheduler is acceptable.

The compatibility suite covers dependency ordering; command and argument delivery; exit status; stdout/stderr and per-attempt logs; timeout, stop, cancellation, and orphan recovery; lock arbitration; working and artifact paths; crash recovery; and replay. It runs against the same vectors in both products. These tests establish neutral execution compatibility only. The analytics adapter allows one process attempt and sets generic retries, review redo and repair rounds to zero, harness fallback to empty and final review to off. Graph node 001 explicitly owns the package source tree, conformance vectors, tested legacy adapter, workspace configuration and local archive/digest outputs. A classified infrastructure retry is a separately recorded execution within the same analytical attempt and reserved budget. Controller admission remains outside the neutral scheduler; no second scheduler, task queue, or worker pool is introduced.

### Computation contract

The portable JSON contract is `AnalyticsRequestV1` → `AnalyticsResultV1`, with `schemaVersion: 1`, a `mode` discriminator, and a mode-specific Pydantic payload. The common binding carries run, inquiry, attempt, execution, Plan revision, exact input and source versions, transformation versions, code digest, Python version, dependency-lock digest, environment digest, and seed. Results echo that binding and contain structured values plus declared output `EvidenceRef`s. They never identify authoritative outputs by path alone.

Pydantic is the normative wire-schema source. Shared conformance vectors are consumed by Python and TypeScript. TypeScript validates against schemas generated from the Pydantic contracts; it does not maintain a second handwritten interpretation of the same fields or mode rules. Node 001 bootstraps the Python package, lock, strict common/mode-specific contracts, generated schemas and conformance vectors before plan registration in 008. The frozen PlanRegistrationV1 union and required fields are defined in interfaces.md: lightweight exploration plus directed descriptive, predictive and two-group inferential profiles. Node 007 implements descriptive count/sum/mean/proportion and denominator lineage, node 013 implements predictive computation and node 006 implements inference using those contracts. Node 001 also owns the shared Python transport/dispatcher. Node 001 owns shared dependency locks; later methods consume the predeclared dependencies. Each execution records the Python version, package lock digest, code and environment digests, seed, exact inputs, and output digests.

## Sidecars, commits, and recovery

No analytics state is added to or serialized into `dag.run.json`. Run-scoped analytics evidence lives under `<run>.d/analytics/`; project-scoped source skills, cards, ledger rationale, pins, and curation live under `<project>/.dag-analytics/`. The neutral run store continues to own the run definition and execution status only.

The version-1 run sidecar has an explicit `schemaVersion`, run ID, monotone manifest revision, commit ID, predecessor revision, and references to immutable records, executions, artifacts, and events. Project memory has its own versioned manifest and revision stream. Record envelopes carry stable IDs, revision, kind, creation time, actor/provenance reference, and content digest. A changed record creates a new revision or linked record; committed history is append-only. Sidecar migrations create new versions and preserve old bytes and references.

One analytics controller is the sole logical writer for a run. It holds a controller-ownership lock across proposal admission, external runner stages, commit, and finalization; the runner's run-file lock remains a distinct neutral concern. Different runs coordinate project memory with a project-level lock and compare-and-swap revision. Workers write only execution-specific staging outputs.

Publication proceeds as follows:

1. Stage under the declared execution ID; do not make staged files reader-visible.
2. Validate the result schema, exact binding, declared output IDs and paths, access, content digests, required-check coverage, and retention limits.
3. Under the sidecar writer lock, compare the caller's `expectedManifestRevision` and stable `commitId` with the current manifest.
4. If the commit ID is already present, return its committed revision as an idempotent replay. If the expected revision lost a race, return `revision_conflict` without overwriting either output.
5. Write immutable record/artifact and commit-revision files, then atomically advance the manifest pointer as the visibility boundary. Only references reachable from a committed manifest are public.

Recovery trusts the committed manifest chain, replays committed events idempotently, and reconciles any materialized event/index projection from that chain. It may remove or quarantine temporary stage material under policy, but it never promotes an orphan or partial output. A durable commit record not yet referenced by the manifest is uncommitted. A lost acknowledgement is resolved by replaying the same commit ID. The append-only commit/event records are authoritative history; the latest manifest and SQLite tables are rebuildable pointers/projections.

Node 009 owns both run and project commit/CAS/recovery primitives and stages.ts, the single-stage production coordinator. Fixed demo 002 owns its synthetic Python fixture and consumes that coordinator before 003 exists; 003 later composes the same stage primitive rather than implementing a second runner. Project facades call commitProjectMemory/recoverProjectMemory; catalog 019 owns its records/index, not a second transaction mechanism. The request/result/recovery models, ID-content collision rule and cross-run reference validation are frozen in interfaces.md. No cross-scope atomicity is promised. Node 003 coordinates idempotent run terminal-intent → committed project memory → run completion acknowledgement; failure at any step leaves completion unacknowledged and recoverable with the same IDs. Later answer/ledger hooks must durably acknowledge before a completed outcome is returned.

Every `EvidenceRef` binds a record ID, revision, and digest. A supported numerical assertion traces to its Claim, Result selector, Check Results, execution/attempt, Plan revision, program/environment locks, transformation versions, source/data version, and output digests. A changed binding makes its prior check insufficient. Reproduction records the declared byte, canonical-table, or numerical-tolerance comparison independently of byte integrity and availability.

## Access, admission, and resource controls

Each local CLI or viewer request resolves an authenticated `Principal` and project-configured roles. Missing identity or grant denies the operation. Source grants name the principal or role, source identity, operation, audience, and effective period. The controller constructs context only after access checks; source adapters check before reads; the catalog filters before ranking; and the viewer checks current access on every metadata and byte request. Retrieval and exposure are recorded. Revocation or redaction retains an access-safe historical pointer without returning protected values.

The local worker may have broader shell access than these TypeScript surfaces. In that profile, grants and write roots are workflow controls, not filesystem isolation. Evaluation partitions readable by that worker are labeled `workflow-withheld; access isolation not enforced`. Stronger guarantees require a tested host, process, or source permission boundary.

Before a worker proposal is scheduled, the controller checks principal/grant, current graph revision and acyclicity, dependencies, exact inputs, output declarations and schema, frozen policy, repeated-work identity, and remaining aggregate reservations. Required checks and final reporting reserve capacity before optional exploration. The fixture allowance is 15 minutes of admitted work, 12 worker invocations, four optional branches, three Plan revisions, and five minutes per command. It enforces the count limits and tested command timeout. A 15-minute value is only a scheduling cutoff unless active cancellation passes the host test. Token, cost, memory, and source-byte limits are estimates or unavailable without enforceable adapter telemetry. Nested tasks reserve aggregate CPU, worker, and source-query capacity where the adapter supports it; a hard-limit-dependent proposal is rejected if the host cannot enforce the limit.

## Incremental record kernel

Every record arrives with its earliest producer and first real consumer. The build does not create a generic record phase without a production path.

| Record or capability | First owner | First consumer |
| --- | --- | --- |
| Principal and SourceGrant | 001 | Worker context, source, catalog, and viewer access checks |
| Evaluation fixture registry and frozen inputs | 18-bootstrap | Feature acceptance fixtures; later evaluator 018 |
| Computation, output, EvidenceRef, commit/recovery | 009 | Evidence publisher and fixed evidence trace 011/002 |
| Frozen check policy and CheckResultV1 | 010 | Claim eligibility and result trace 011 |
| Report assertion and evidence trace | 011 | Browser viewer and fixed-analysis path 002 |
| Source/data and transformation versions | 007 | Predictive comparison 013 and inquiry 003 |
| Question, Plan revision, exposure | 008 | Admission 012 and inquiry 003 |
| Budget/proposal admission and analytical identity predicate | 012 | No-progress admission; shared predicate reused by ledger 023 |
| Descriptive calculation and denominator payload | 007 | Directed/exploratory descriptive answers through 003 |
| Predictive request/result | 013 | Typed TypeScript façade and inquiry 003 |
| Artifact identity and rebuildable catalog | 019 | Ledger 023 and answer search 020 |
| Durable attempt and match assessment | 023 | Answer orientation 020 |
| Answer card, retrieval, exposure | 020 | Later inquiry orientation and skill capture/activation |
| Source skill and compatibility | 014/015 | Second inquiry 004 and recurrence 016 |
| Window comparison and dependency invalidation | 016 | Recurrence/use path 005 and inferential profile 006 |
| Use Assessment and Decision | 017 | Recurrence/use path 005 |
| Inferential request/result | 006 | Its answer/trace path and evaluator 018 |
| Skill candidate and activation event | 021/022 | Later skill use in 022 and inquiry 004 |
| Reference evaluator and release assessment | 18-bootstrap/018 | Feature tests as they land, then convergence 99 |

### Single owner for analytical identity

Node 012 owns `src/analytics/analytical-identity.ts` and the typed `AttemptIdentityV1` dimensions. Its `compareAnalyticalIdentityV1(left, right)` function is implemented once and used first for 012 no-progress admission. Every required dimension must be known and equal to establish an exact identity; an unknown dimension returns unresolved. Node 023 imports this predicate, owns the durable attempt entries and `AnalyticalMatchV1` classification (`exact`, `related`, `incompatible`, `unresolved`), and adds semantic question rationale/provenance. It does not implement a second identity comparison. Node 020 calls the ledger match once during orientation and then performs its own answer-card ranking.

## Viewer and artifact serving

The answer is the first screen; runtime details sit behind the evidence inspector. A report value opens `ReportAssertion → Claim → Result → Check Results/Review → Execution → Transformation → Dataset version`. Missing, stale, restricted, redacted, or unavailable links remain visible with the permitted reason. There is no generic “verified” status.

The viewer accepts stable record/artifact IDs, never worker-supplied filesystem paths as authority. Resolve an ID through a committed manifest, require its current grant, canonicalize the requested artifact and approved root, reject traversal and symlink escape, then serve declared bytes only. Escape untrusted report/source text and serve active content with safe content handling. Metadata-only readers receive permitted metadata and limits, not raw rows or restricted paths. A broken or denied link must not reveal the protected path or value.

## Evaluation and release boundary

All demonstrations and worker evaluations use synthetic or local fixtures. Node 18-bootstrap owns the versioned fixture registry and lock; feature 018 owns evaluator and release assessment. Reference answers, relevance labels, and planted-fault explanations are read through a separate evaluator interface only after the worker responds, and are absent from worker context compilation. Context-leak fixtures assert zero reference-key/fault-description bytes in the actual worker packet and digest. Because a local shell may read fixture files, the product labels this profile workflow-withheld and makes no enforced-holdout claim.

The live user path records model `gpt-6-luna`, reasoning `xhigh`, harness and version, prompt/context and source digests, budgets, and artifact IDs. Feature 018 consumes the accepted thresholds and denominators from its locked specification, reports fault recall with false blocking, and labels a 20-case pass eligible only for the named fixture profile. No accepted answer, label, threshold, or checklist is changed by implementation results.

## Graph-owned facades

Node 00 owns only the three architecture outputs and the stage verification record. Every implementation file remains owned by its existing row in `graph.json`; the matching facade and interface owners are summarized in [interfaces.md](interfaces.md), and node dependencies and end-to-end checks are enumerated in [acceptance-plan.md](acceptance-plan.md). Shared toolchain files have one owner: node 001 owns the TypeScript package manifest/lock changes required for the neutral package adapter; node 001 also owns `python/pyproject.toml`, `python/uv.lock`, common mode contracts and generated schemas; 007, 013 and 006 own their computation modules. Node 001 owns the top-level `src/commands/index.ts` registry, including factory-route removal and a computed lazy analytics trampoline that builds before 003 exists. Node 003 owns the typed analytics extension registry, lifecycle and `src/analytics/cli.ts` subcommand dispatch; it exports `analyticsCmd(argv, cmd)` for the 001 trampoline and tests the live route. Later features export providers from their own facades for automatic recall, ledger events, source discovery, skills, recurrence and authorization; their production paths do not require edits to another node's controller. The fixed allowlist, dependency order, error propagation and incremental capability rules are frozen in interfaces.md. Node 18-bootstrap owns the fixture registry, fixture lock, and base evaluator API; contributors add only their feature-scoped cases. Each feature owns its acceptance test and its Spec Kit plan/tasks/verification outputs. Node 018 owns evaluator/release implementation, not primary domain T-case ownership. Integration nodes exercise producer, consumer, viewer, and failure paths without taking ownership from focused features.

The original `src/factory/` modules and existing run viewer are migration context, not analytics architecture. The analytics worker context excludes their prompts, task templates, and software-review semantics. Neutral execution concepts stay generic; analytical state and decisions live under `src/analytics/` and its sidecars.

## Implementation dependency correction — 2026-09-29

Source discovery 014 produces candidate packages. Feature 015 implements compatibility, usage, refresh and historical validity; its early unit tests may consume frozen, independently validated historical active records, but cannot implement activation or present seeded records as a live activation demonstration. Candidate capture 021 consumes completed 003 inquiries, 020 answer memory and 014/015 source providers; it does not require the later 004 demonstration. Feature 022 owns production source/method/rule activation and independent validation. Build 021 and 022 before 004, which then exercises actual first-inquiry candidate → 022 validation/activation → second-inquiry reuse. No demo shortcut can create an active record. This changes implementation sequencing only; accepted product requirements, state vocabulary and release gates remain unchanged.

Feature 008 validates its early registration/lock/exposure production facade using the already built 009 single-stage coordinator. It must not require the later 003 delegated controller to pass an earlier unit. Full controller integration and actual context-supply/observation paths are verified by 003, consuming the exact 008 APIs and their durable records. Early fixtures distinguish a production stage/facade invocation from a complete delegated inquiry.

The 014 cold-discovery hook checks only that there are zero accessible active source-skill records for the exact source identity/environment/tenant. It does not call 015's later compatibility predicate or require an undefined 003 service. When active refs exist, 014 defers; 015 alone decides compatibility and reports unresolved mismatch. The explicit source-discover command handles requested refresh. This keeps the 014 acceptance runnable before 015 and avoids a cyclic dependency.
