# Data Model: Catalog and Manage Analytical Artifacts

This is the feature-owned model layered on node-009's immutable project and run manifests. It does not add a persistence engine. State values reuse [the canonical vocabulary](../../docs/specs/state-vocabularies.md); search rows and SQLite are projections.

## Artifact record

`ArtifactRecordV1` is the stable reference for a committed output or a recorded import version.

| Field | Meaning and validation |
| --- | --- |
| `schemaVersion` | `1`. |
| `artifactId` | Stable opaque ID persisted in its authoritative manifest before indexing. Never derive it from a path or digest. |
| `revision` | Positive immutable record revision. Changed bytes, role, retention class, scope, or provenance create a new revision/record with a supersession relation. |
| `contentDigest` | SHA-256 of the observed bytes. A missing/redacted source keeps the historical digest in the protected record. |
| `mediaType` | Content-detected media type, not an untrusted filename claim. |
| `role` | One of `input_extract`, `intermediate_table`, `plot`, `model`, `diagnostic`, `result_table`, `report`, `other`, `unknown_imported`. Imported role defaults to `unknown_imported`; filename is not evidence of use. |
| `retentionClass` | Exactly `scratch`, `cache`, `evidence`, or `deliverable`. |
| `origin` | Producer/import reference and explicit origin kind. Imported files without traceable execution use `unknown` provenance. |
| `producerRef` / `importRef` | Exact run/execution record or durable import record; only the applicable reference is populated. |
| `availability` | `available`, `unavailable`, or `redacted`, with a permitted reason and effective time. |
| `accessScope` | Source/project identity, audience and grant scope; it is checked at each search/read. |
| `usageLinks` | Exact Result, Claim, Report, and answer-card refs that consume the artifact. Links cannot be inferred from a name. |
| `createdAt` | Controller-recorded timestamp. |

Committed outputs use IDs and producer/output declarations from the committed run manifest. Staged and failed outputs are not `ArtifactRecordV1` results; an inventory listing may represent them as `CatalogEntryV1` diagnostics with a separate `sourceClass`, execution ref and uncommitted state. Imported artifacts are committed project records with `sourceClass: imported_file`, `role: unknown_imported` unless later evidence-backed curation creates a new revision, and no execution ref by implication.

## Import record and duplicate group

`ImportRecordV1` is an immutable observation of one user-selected original path under one approved root. It records an opaque `importId`, path-specific `artifactId`, source-root ref, protected path locator, digest/size/modified time/media type when observed, permitted schema/profile metadata, access/grant refs, import time, current version relation and stop/availability reason. It never moves or copies the source by default. A source with no traceable execution is `origin: unknown`.

Each path receives its own artifact ID, even when its bytes match another path. A `DuplicateGroupV1` groups visible `artifactId`s by project-scoped digest. Duplicate membership is filtered by current audience before a group or count is returned. A change creates a new immutable artifact version linked to the prior `importId`, appends a source-change event, and marks dependent current projections `reassessment_required` until an owner resolves them; historical records remain intact. A missing source changes availability to `unavailable` without rewriting provenance and retains the prior identity/metadata. Availability and current validity remain separate dimensions.

## Usage and curation records

`ArtifactUsageLinkV1` identifies the consuming record kind, stable ID, revision and digest. Adding/removing a relationship is a durable curation event, not a mutable SQLite edit. Curation may record a project-owner pin, permitted label, grouping or evidence-backed usage link. Role and retention changes create a new artifact record version; a pin or annotation never changes claim support or authors an answer card.

`CatalogEntryV1` is the safe read projection used by search/list views. It holds stable refs, current availability and source class (`committed_output`, `staged_output`, `failed_output`, `imported_file`), plus only permitted metadata. It cannot make an uncommitted artifact authoritative or promote an imported file to an answer.

## Search index

The SQLite FTS5 projection has a schema version and indexes stable IDs, safe titles/tags, allowed schema names and access-safe summaries. Raw rows, cell values, restricted titles, unrestricted filesystem paths, credentials and reference answers are excluded. Structured scope and grant predicates are applied before lexical query/ranking. Index rows are recreated from committed run manifests, recovered project records and durable curation events; no index row establishes provenance.

## Output, notebook and retention records

`OutputDeclarationV1` binds one exact execution/producer, stable artifact ID, canonical role, retention class, approved root, per-item byte cap, batch count/byte cap and any allowed media type. Admission rejects an undeclared role/class, absent limit, count/byte overflow, wrong root or stale/missing committed Result before publication.

`ExportRecordV1` points to a committed Result revision and durable answer-card revision, the declared export format, generated artifact ref, principal grant, output-policy revision and exact Result-to-export mapping. The Result remains independently accessible. Default CSV/XLSX export allowance is zero.

`NotebookReplayV1` stores notebook artifact/ref digest, exact input artifact refs, execution/code/environment and dependency-lock refs, Python version, ordered code-cell IDs and digests, fresh-kernel/clean-rerun outcome, rerun comparison, and Result IDs bound to each displayed Claim. A valid publishable notebook requires all fields and a successful clean replay. Hidden-state dependency, missing binding, unsupported cell syntax, or failed replay leaves the notebook `exploratory` and supports zero Claims.

`RetentionIntentV1` is an append-only project event that enumerates approved managed scratch/eligible cache candidates and the policy/dependency check. Per-path completion records distinguish planned from deleted. Recovery reconciles remaining managed files against the same intent. Evidence, deliverables, imports, user-owned paths, and scratch with dependent evidence links are never deletion candidates.

## Relationships and invariants

```text
Committed run manifest ──produces──> ArtifactRecordV1 ──used by──> Result/Claim/Report
Project import record ──observes───> ArtifactRecordV1 ──grouped by digest──> DuplicateGroupV1
Project curation event ──links/pins─> ArtifactRecordV1
Committed sources + curation ──────> rebuildable FTS5 index
OutputDeclarationV1 ──bounds───────> ExportRecordV1 / NotebookReplayV1
RetentionIntentV1 ──deletes only───> managed scratch or eligible cache
```

- Manifest commit and digest validation precede search visibility.
- Current grant checks precede metadata matching and byte reads.
- Unknown values remain unknown; unknown origin is never converted to no origin.
- Duplicate grouping retains each original path identity and respects access scope.
- A missing/changed file creates visible current state without erasing historical bytes/metadata.
- Index rebuild is deterministic with respect to record IDs/scopes/links and does not create records.
- Negative, failed, null, incomplete and inconclusive analytical evidence remains visible with its actual status.
