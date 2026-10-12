# Research: Publish and Recover Exact Evidence

The frozen architecture, interface file and feature specification resolve the implementation choices for this slice. No external research or unresolved clarification is required.

## Decisions

### One scoped append-only/CAS primitive

**Decision**: Implement a shared internal primitive in `src/analytics/store.ts`, parameterized by scope kind and scope ID. Keep run and project manifests, locks and revision chains separate. `publication.ts` exports the frozen run and project APIs.

**Rationale**: The architecture assigns both persistence boundaries to node 009 and explicitly requires one shared primitive. Separate public manifests preserve independent run/project ownership and the no-cross-scope-atomicity rule. Per-scope locking plus an expected-revision check gives same-scope races a deterministic winner and typed `revision_conflict` for losers.

**Alternatives considered**: Separate run and project implementations were rejected because replay, collision, crash and recovery behavior could diverge. A single global lock was rejected because unrelated scopes should not block each other.

### Immutable append files with manifest-pointer visibility

**Decision**: Write immutable record/artifact/commit files first, then atomically replace the scope's current manifest pointer as the visibility boundary. Record the predecessor revision and digest in each committed revision. Recovery walks and validates the committed chain, replays derived events idempotently, reports orphan/staging material, and never promotes it.

**Rationale**: This follows the sidecar architecture and gives readers one authoritative committed head. A crash before pointer advance leaves only invisible staged/orphan files; a lost acknowledgement after pointer advance is resolved by replaying the original commit ID and canonical request digest.

**Alternatives considered**: Treating any durable commit file as public was rejected because a pre-pointer crash would expose partial work. Mutating existing records or editing the manifest in place without atomic replacement was rejected because it loses prior history or creates partially visible JSON.

### Local sidecars remain distinct from neutral run state

**Decision**: Use `<run>.d/analytics/` for run evidence and `<project>/.dag-analytics/` for project memory, with explicit `schemaVersion`. Leave `dag.run.json` under neutral execution ownership.

**Rationale**: These roots and the neutrality boundary are fixed by `ARCHITECTURE.md`; the project stream must be usable by later memory/catalog facades without changing the execution engine's run definition/status model.

**Alternatives considered**: Adding analytics records to `dag.run.json` or the neutral runner's storage schema was rejected as a silent cross-product contract change.

### Evidence identity and comparison are independent dimensions

**Decision**: Validate the exact immutable Plan, input/source, transformation, program, environment, attempt and execution bindings before publication. Verify declared artifact IDs, paths, size and byte digests before commit. Reproduction records a declared exact-byte, canonical-table or numeric-tolerance rule and one canonical reproduction state; current artifact availability is recorded separately.

**Rationale**: Byte integrity detects altered bytes, while canonical or tolerance comparison can distinguish value-preserving serialization from numerical mismatch. Missing/stale identity cannot inherit a prior check. Redaction affects current access/availability without erasing historic existence or rewriting the prior reproduction result.

**Alternatives considered**: Path-only binding and byte-only reproduction were rejected because paths can be reused and serialization may change without value change. Treating redaction as deletion or reproduction success was rejected by the state vocabulary.

### Stage execution delegates to node 001

**Decision**: `executeStageV1` accepts the frozen StageRequest fields and uses the existing node-001 analytics adapter/neutral package for one argument-vector invocation. It validates output and binding before any commit and does not retry a valid analytical result.

**Rationale**: Node 009 owns the coordinator, while node 001 owns process launch, scheduling, logs, timeouts and neutral recovery. This lets 002 use the stage before 003's inquiry controller exists without creating a second runner.

**Alternatives considered**: Launching child processes directly from `stages.ts`, using a new scheduler, or composing generic retries was rejected by the frozen ownership boundary.

### Native test runner and synthetic cases

**Decision**: Keep acceptance cases in `src/analytics/acceptance/009.test.ts` using the repository's existing Node native test runner and local `tsx` loader. Test hooks simulate interruption at explicit publication boundaries with temporary directories and synthetic records/artifact bytes.

**Rationale**: `package.json` already provides `tsx`, `node:test` and a `pnpm test` build/test script. The graph assigns one 009 test file; pattern-filtered commands make run-store and project-store race/recovery checks directly runnable in their owning units.

**Alternatives considered**: Adding Jest/Vitest or a second fixture framework was rejected because no such framework is installed or needed. Live user data and external services are unnecessary for deterministic commit invariants.

## Decisions inherited from the freeze

- Commit statuses are `committed`, `replayed`, `revision_conflict`, and `rejected`.
- Artifact availability is `available`, `unavailable`, or `redacted`; reproduction is `not_attempted`, `reproduced`, `mismatched`, or `unavailable`.
- A commit ID plus an unchanged canonical request digest replays the existing revision; changed content under the same ID is rejected.
- A new commit ID with a stale expected revision does not overwrite and returns `revision_conflict`.
- Cross-run references in project memory must resolve to committed run history.
- The canonical execution binding retains Python version, dependency-lock digest, environment digest, code digest, seed, and exact input/Plan/source/transformation refs where applicable.
