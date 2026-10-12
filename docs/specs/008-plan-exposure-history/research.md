# Research: Plan locking and durable exposure

This is a bounded contract review, not external technology research. The proposal, accepted feature spec, canonical vocabularies, access/judgment contract, frozen interface freeze, architecture, graph, and feature 009/003 ownership were read before design. No unresolved clarification remains.

## Decision: Use the generated, mode-specific Plan schemas

**Decision**: Validate `PlanRegistrationV1` against feature 001's generated Pydantic schemas and shared TypeScript conformance vectors. Accept only exploratory, descriptive, predictive, and inferential profiles. A causal or other unsupported profile returns an explicit limitation.

**Rationale**: `docs/build/interfaces.md` makes Pydantic the normative wire-schema source and assigns 008 registration/locking, while 001 owns generation and conformance. Directed plans carry population, target, comparison, partition design, search space, stopping rule, required-check policy, intended use, measurements, exposure boundary, and uncertainty. Exploratory registration remains lightweight.

**Alternatives considered**: A second handwritten TypeScript schema would diverge from the frozen owner contract. A generic arbitrary-JSON plan would admit missing/extra analytical dimensions and is prohibited by the strict interface.

## Decision: Derive substantive identity from frozen analytical fields

**Decision**: Compare the locked registration fields for population, target, comparison/method, partition design, search space, and stopping rule. Treat a changed value as substantive; require a linked Plan revision and an attributed worker reason. A code digest change with unchanged fields creates a new Execution only. If the semantic classification cannot be resolved from the mechanical diff and reason, retain `unresolved` and do not grant `planned_confirmatory`.

**Rationale**: FR-008 names population, target, method, partition design, search space, and stopping rule; the predictive and inferential registration contracts bind the comparison and evaluation rules into these fields. A content digest identifies the immutable payload but does not decide which change is analytical. The controller owns deterministic field comparison; the worker owns its semantic explanation.

**Alternatives considered**: Comparing only a whole-payload digest would incorrectly treat code changes, author changes, and analytical changes alike. Accepting a worker's “code-only” label without comparing locked fields would trust the proposer at the judgment boundary.

## Decision: Use the canonical interpretation values without adding aliases

**Decision**: Derive only `planned_confirmatory`, `exploratory`, `outcome_informed`, or `exposure_unknown`. Preserve each historical Plan label. A locked plan is `planned_confirmatory` only when the relevant inspection rule preceded relevant exposure and exposure history is sufficiently known. Unplanned work is `exploratory`; a known post-outcome substantive fork is `outcome_informed`; incomplete or unknown access is `exposure_unknown`.

**Rationale**: `docs/specs/state-vocabularies.md` is normative. The status is a scoped interpretation of plan and exposure history, not a method-quality or execution state. Review approval cannot change history. A separate truly enforced fresh partition can establish a new linked confirmation while retaining exploratory origin.

**Alternatives considered**: A boolean `preregistered` loses the distinction between unknown, exploratory, and post-observation work. A global inquiry badge incorrectly treats every later independent comparison as automatically confirmed or contaminated.

## Decision: Append an exposure record before the result is delivered

**Decision**: Record actor, material seen, source or partition, time, observation basis (`observed`, `self_reported`, or `unknown`), and evidence refs. Commit before returning relevant outcomes or outcome-bearing skill/summary material in the worker context. Record workflow withholding and its host guarantee as such; do not infer no access from no log entry.

**Rationale**: FR-003 and proposal §8 define the required event facts and distinguish observed from self-reported access. The access contract places exposure recording at retrieval/context construction. A commit-before-release sequence makes the log durable before the worker can see the outcome.

**Alternatives considered**: Logging exposure after a computation leaves a crash window in which the worker saw a result but history says it did not. Treating absent logs as zero exposure contradicts I08 and the accepted assumptions about local filesystem access.

## Decision: Rehydrate from committed controller history, not a new 009 API

**Decision**: `exposure.ts` accepts a typed committed-history snapshot supplied by the controller's existing committed-read context service. The controller obtains the committed head through 009 recovery, resolves only refs reachable from that committed manifest, and supplies the envelopes/summaries to 008. Feature code verifies their exact IDs, revisions, digests, event order, and inquiry ownership. It never scans sidecar paths or treats an orphan as history.

**Rationale**: 009 owns manifest/CAS/recovery and `docs/build/interfaces.md` gives the 003 invocation context committed reads. The existing interface does not name a separate `readCommittedRecord` function. Consuming the host-provided read capability avoids an invented 009 public API while making recovery input explicit and testable.

**Alternatives considered**: A new 008 filesystem reader would bypass 009's committed visibility boundary. Adding an API to 009 or editing the frozen interfaces would exceed this feature's ownership. Depending directly on 003's source would create a cycle because 003 consumes 008.

## Decision: Test the 008 facade and 009 stage path at the feature boundary

**Decision**: Feature 008 exports typed request/result hooks for registration, locking/change assessment, commit-before-release exposure, and committed-history rehydration. Its acceptance calls those production facade hooks with synthetic/local fixtures, uses the actual 009 commit/recovery services and `executeStageV1`, and verifies rejected/conflicting exposure commits return no permit and cause no stage invocation. It validates the rehydration projection at the 008 boundary. Feature 003 composes these hooks and owns the assertions that outcome-bearing material is supplied to an actual worker and that a replacement worker receives the rehydrated history.

**Rationale**: The accepted graph makes 003 downstream of 008 and assigns its controller files to 003. Importing or simulating that controller would violate ownership and test a fabricated integration. The production 008 facade plus actual 009 publication and stage services exercises the available feature boundary, including the critical release gate; the dependent 003 integration verifies actual context delivery without changing the graph.

**Alternatives considered**: Moving full-controller acceptance into 008 would create a graph cycle or require implementation of 003-owned files. A helper-only unit test would miss durable commit ordering and the no-permit/no-stage-call failure path. A later live 003 inquiry is not a prerequisite for this earlier feature.

## Decision: Report local access honestly

**Decision**: Preserve a guarantee descriptor on exposure events and plan boundaries. Fixture partitions readable by the local worker are marked `workflow-withheld; access isolation not enforced`. Unknown out-of-tool access stays unknown; only a tested separate host/source boundary can establish enforced no-access.

**Rationale**: Proposal I13 and the access contract limit guarantees to what the environment observes or enforces. The evaluation fixture registry is owned by 18-bootstrap and its expected results remain separate from worker context.

**Alternatives considered**: Calling a partition “held out” merely because the controller delayed it would overstate independence. Editing fixture labels or thresholds to make a test pass is not permitted.
