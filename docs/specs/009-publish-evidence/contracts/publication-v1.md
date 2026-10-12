# Publication and Stage Contracts (Version 1)

The wire names and frozen fields below come from `docs/build/interfaces.md`. `src/analytics/publication.ts` and `src/analytics/stages.ts` implement these APIs; `src/analytics/store.ts` owns the shared scoped persistence mechanism. Inputs reject unknown fields and missing required identity fields at authoritative ingress. Analytics-only records remain in sidecars.

## Commit outcomes

```ts
type CommitStatusV1 =
  | 'committed'
  | 'replayed'
  | 'revision_conflict'
  | 'rejected';
```

`revision_conflict` never overwrites the current head. `replayed` returns the original committed revision when the stable commit ID and canonical request digest match. Reusing an ID with changed content is rejected with a bounded reason.

## Run evidence

```ts
commitEvidence(request: CommitRequestV1): Promise<CommitResultV1>;
recoverEvidence(runId: string): Promise<RecoveryReportV1>;
```

`CommitRequestV1` uses the frozen fields: `schemaVersion`, `commitId`, `runId`, `expectedManifestRevision`, principal ref, execution/attempt refs, immutable record/artifact refs, validation decision refs and commit timestamp. `RunManifestV1` carries `schemaVersion`, `runId`, revision and predecessor, commit ID, committed record/artifact/event refs and digest.

Before visibility, validate exact Plan/program/environment/source/attempt/execution binding; declared output identity, path within the execution staging/output root, size and byte digest; validation decision subjects; and the principal's current write authorization at the publication boundary. Do not rely on a grant snapshot captured when work was staged. A missing/denied or since-revoked grant rejects publication without advancing the manifest pointer or exposing staged records. A successful process exit is not evidence acceptance. The reader-visible boundary is the atomically advanced manifest pointer.

## Project memory

```ts
commitProjectMemory(
  request: ProjectCommitRequestV1,
): Promise<ProjectCommitResultV1>;
recoverProjectMemory(projectId: string): Promise<RecoveryReportV1>;
```

`ProjectManifestV1` carries `schemaVersion`, `projectId`, revision and predecessor, commit ID, committed record/event refs, stable card/artifact/skill IDs and scope/availability projection refs. `ProjectCommitRequestV1` carries `schemaVersion`, `commitId`, `projectId`, `expectedManifestRevision`, principal ref, immutable records/event refs, optional committed run-manifest ref and timestamp. A present run ref must resolve through a committed run manifest. Before visibility, recheck the principal's current project-write authorization and current access to referenced run evidence; a missing/denied or since-revoked grant rejects the commit without advancing the project manifest. `ProjectCommitResultV1` returns status, committed/current revision, committed project manifest ref when present and bounded reason. `RecoveryReportV1` is shared by both scopes and never promotes incomplete files.

## Reproduction and availability

The reproduction assessment records `not_attempted`, `reproduced`, `mismatched`, or `unavailable` and names one comparison method: exact bytes, canonical table values, or numerical tolerance. The method parameters and observed agreement are durable assessment content. Availability is independently `available`, `unavailable`, or `redacted`. Access checks run before metadata or bytes are returned; redaction retains safe historical identity and an availability reason/effective time.

## Single-stage coordinator

```ts
executeStageV1(
  request: StageRequestV1,
  services: StageServicesV1,
): Promise<StageResultV1>;
```

`StageRequestV1` carries stage ID, principal, execution/attempt IDs, immutable Plan/input/program/environment refs, executable/argv, timeout and declared output schema/path/digests. `StageResultV1` carries execution ref, structured output refs and publication outcome. Services adapt to node 001's existing runner adapter; there is no direct child-process launcher here. One stage is invoked, its execution is recorded, its binding and outputs are validated, and only then is publication attempted. Analytical failures and negative outcomes are recorded; no favorable-result retry is initiated.

## Visibility and recovery rules

1. Write candidate bytes and immutable records in execution-specific staging locations.
2. Validate schema, refs, digests, access, sizes, declarations and expected revision.
3. Under the per-scope writer lock, detect same-ID replay/collision and compare expected revision.
4. Persist immutable commit/revision content and atomically advance only the scoped manifest pointer.
5. Recovery derives public state from the committed chain. It may report or safely clean incomplete staging material but cannot make it committed.

Run and project commits are not cross-scope atomic. A project commit referring to a run is admitted only after the referenced run revision is committed.
