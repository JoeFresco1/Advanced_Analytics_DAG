# Data Model: Publish and Recover Exact Evidence

This feature implements the version-1 types frozen in `docs/build/interfaces.md`. It does not introduce alternate machine states or change accepted specifications.

## Core records and relationships

### Exact computation binding

An accepted Result points to a single execution and attempt and carries exact immutable references to its Plan revision, source/data versions, transformations, program/code digest and environment. The computation binding includes `runId`, `inquiryId`, `attemptId`, `executionId`, `planRef`, `sourceRefs`, `transformationRefs`, `programDigest`, `pythonVersion`, `dependencyLockDigest`, `environmentDigest`, and `seed`. Refs carry `recordId`, positive revision and content digest. Mutable names or file paths do not substitute for refs.

### Produced artifact and evidence reference

`ProducedArtifactV1` identifies a staged artifact by `artifactId`, `relativePath`, digest, media type and size. Commit creates an `EvidenceRef`, which adds the artifact ID to an immutable record ref. The path locates bytes under the declared staging/output root; identity is the stable ID, revision and digest. A declared output is required before a computation can be accepted. Exit code zero with a missing output is an evidence error.

Artifact availability is `available`, `unavailable`, or `redacted`, accompanied by a permitted reason and effective time. Redaction creates an append-only availability event/version and access-safe pointer; it does not erase the historic artifact record or expose protected bytes.

### Run manifest and commit

`RunManifestV1` contains `schemaVersion`, `runId`, monotone `revision`, `previousRevision`, `commitId`, committed record/artifact/event refs and digest. `CommitRequestV1` contains `schemaVersion`, stable `commitId`, `runId`, `expectedManifestRevision`, principal ref, execution/attempt refs, immutable record/artifact refs, validation decision refs and commit time. `CommitResultV1` contains the request commit ID, status, committed/current revision, committed manifest ref when present and a bounded reason.

The manifest pointer selects the only reader-visible revision. A commit record not reachable from the committed chain remains uncommitted. Each committed record and event remains immutable; a subsequent change is appended as a linked record or revision.

### Project manifest and commit

`ProjectManifestV1` contains `schemaVersion`, `projectId`, monotone `revision`, `previousRevision`, `commitId`, committed record/event refs, stable card/artifact/skill IDs and scope/availability projection refs. `ProjectCommitRequestV1` contains `schemaVersion`, stable `commitId`, `projectId`, `expectedManifestRevision`, principal ref, immutable records/event refs, optional committed run-manifest ref and timestamp. `ProjectCommitResultV1` returns the canonical commit status, committed/current revision, manifest ref when present and bounded reason.

If the optional run-manifest ref is present, it must resolve to a committed run revision and pass current access checks before the project write can publish. The controller also checks current project-write authorization at publication rather than relying on staging-time grants. A denied or revoked grant leaves the project head unchanged. There is no transaction across run and project streams.

### Recovery report

`RecoveryReportV1` identifies scope ID/kind and reports the committed head revision/ref, replayed commit IDs, ignored staging/orphan IDs, unresolved/corrupt refs and safe cleanup actions. Recovery may validate committed history, replay committed events idempotently and reconcile projections. It cannot adopt an unreferenced record or artifact as evidence.

### Reproduction assessment

Reproduction state is `not_attempted`, `reproduced`, `mismatched`, or `unavailable`. An attempted assessment records a declared comparison rule:

| Rule | Comparison |
| --- | --- |
| Exact bytes | Compare the declared artifact digests/bytes. |
| Canonical table | Parse and canonicalize declared table values and schema before comparing. |
| Numerical tolerance | Compare the declared numeric selectors using a predeclared tolerance and record observed agreement. |

Byte integrity remains recorded independently. A byte difference with equal canonical values can be `reproduced` under a semantic rule; values outside a numeric tolerance are `mismatched`. Required bytes or inputs that cannot be read give `unavailable`. Current redaction is not retroactively converted into a reproduced result.

### Stage request and result

`StageRequestV1` freezes the stage ID, principal, execution/attempt IDs, immutable Plan/input/program/environment refs, executable/argument vector, timeout, and declared output schema/path/digests. `StageResultV1` returns the execution ref, structured output refs and publication outcome. The coordinator delegates process mechanics to node 001, validates exact binding and declared outputs, and records the execution before publication. A negative computation or failed check is retained without a favorable-result retry.

## Persistence boundaries

| Scope | Root | Lock/CAS key | Authoritative visibility head |
| --- | --- | --- | --- |
| Run evidence | `<run>.d/analytics/` | Run ID and expected run-manifest revision | Current `RunManifestV1` pointer |
| Project memory | `<project>/.dag-analytics/` | Project ID and expected project-manifest revision | Current `ProjectManifestV1` pointer |

The two scopes use the same internal append/CAS algorithm and separate locks and revision chains. `dag.run.json` remains neutral execution storage. A projection or search index is never authoritative for commit state.

## Invariants

1. A committed Result's binding and artifact digests are exact and immutable.
2. Every commit compares the expected revision under the owning scope lock.
3. One stable ID with the same canonical request digest is an idempotent replay; the same ID with changed content is rejected.
4. Different commits at one expected revision cannot both become visible.
5. A missing declared output, changed binding or invalid digest cannot produce a committed Result.
6. Recovery trusts the committed manifest chain, never orphan staging or commit files.
7. Project memory cannot expose a reference to an uncommitted run revision.
8. Availability and reproduction are separate facts; redaction preserves permitted historical links.
