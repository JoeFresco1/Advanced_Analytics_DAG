# Artifact Catalog Contract V1

## Authority and ownership

`catalog.ts` owns `ArtifactRecordV1`, safe inventory/search projections, manifest reconciliation and the typed `analyticsExtensionV1` provider. Run artifacts are read from node-009 committed run manifests. Imports, curation, answer-card projections, ledger projections, and retention records are read from or written through node-009 project memory; recovery consumes node-009 `recoverProjectMemory`. Neither SQLite nor a command response is authoritative. `catalog.ts` does not author answer cards, decide claim support, classify ledger matches, or duplicate node-009 CAS.

## Stable artifact fields

The implementation follows the frozen `ArtifactRecordV1` contract in `docs/build/interfaces.md`: stable `artifactId`, byte `contentDigest`, `mediaType`, canonical `role`, canonical `retentionClass`, `origin`, producer/import reference, `availability`, `accessScope`, and exact usage links. IDs remain stable across index rebuilds. IDs for distinct source paths remain distinct even for identical bytes. A committed role or retention change creates an immutable revision/event; the worker cannot silently override a role after commit.

Canonical roles: `input_extract`, `intermediate_table`, `plot`, `model`, `diagnostic`, `result_table`, `report`, `other`, `unknown_imported`.

Canonical retention classes: `scratch`, `cache`, `evidence`, `deliverable`.

Canonical availability: `available`, `unavailable`, `redacted`, with access-safe reason and effective time.

Imported artifacts without traceable execution use `unknown_imported` and `origin: unknown`; names such as `final.xlsx` do not produce a Result, Claim, Report or answer card.

## Search and rebuild operations

`rebuildIndex(projectId)` replays every committed revision in the recovered 009 project and run manifest chains, then atomically replaces the derived FTS5 index. It projects committed artifacts, imports, curation, answer-card records, and versioned ledger-attempt, rationale, and match-assessment records. It preserves stable record/artifact IDs, canonical source bytes and refs, access scopes, availability, current validity, artifact usage links, card/artifact links, and ledger rationale/match-assessment references. Staging/orphan records are excluded from authoritative search. Rebuilding changes only the disposable index; it does not rewrite, reserialize, or recommit authoritative records. An optional inventory diagnostic may show staged/failed source class when current access permits, but its state is explicit and is not a committed result.

### Versioned committed-record projection input

`CatalogProjectionInputV1` is the typed, rebuildable search contribution stored as a committed 009 project record by the owning answer-memory or ledger feature. It is a projection envelope, not a second domain record or CAS protocol; artifact and import entries are projected from their native committed records:

```ts
type CatalogProjectionInputV1 = {
  schemaVersion: 1;
  recordKind: 'answer_card' | 'ledger_attempt' | 'ledger_rationale' | 'ledger_match_assessment';
  recordRef: RecordRefV1;
  commitRef: RecordRefV1;
  accessScope: ArtifactRecordV1['accessScope'];
  availability: 'available' | 'unavailable' | 'redacted';
  currentValidity: 'current' | 'reassessment_required' | 'superseded' | null;
  safeSearchText: string[];
  artifactRefs: EvidenceRef[];
  answerCardRefs: RecordRefV1[];
  attemptRefs: RecordRefV1[];
  rationaleRefs: RecordRefV1[];
  matchAssessmentRefs: RecordRefV1[];
};
```

The feature-owned ingestion entrypoint is `ingestCommittedProjectionV1(input: CatalogProjectionInputV1): Promise<void>`. It accepts an input only when `recordRef` and `commitRef` resolve to committed history recovered through 009, and applies it idempotently to the disposable projection. Version-1 `ledger_attempt`, `ledger_rationale`, and `ledger_match_assessment` contributions retain their exact attempt, rationale, and match refs plus the owning feature's access and current-validity values. The ledger owner supplies the committed, access-safe rationale text; catalog ingestion does not invent rationale, compare analytical identity, assign match state, create attempts, or rank answer cards. The reader uses this stable envelope and record refs without importing `ledger.ts` or requiring feature 023 to exist at build time. Unsupported projection versions remain excluded and are reported as bounded diagnostics.

`searchAccessible(principal, query, filters)` validates the current principal and grant; applies access-scope, source, audience, status and metadata predicates before FTS query/ranking; then returns permitted artifact IDs and permitted summaries. It returns no restricted title, protected path, row/cell content, or cross-scope duplicate count. A metadata-only grant sees only allowed metadata, not bytes. A denied lookup may be recorded as an access-safe event without disclosing the denied artifact name or content.

Search operates over artifact inventory. It does not create an answer card, infer that a matching filename is an answer, or conflate a file match with `direct_answer_found`.

## Typed extension provider

`catalog.ts` exports:

```ts
export const analyticsExtensionV1: AnalyticsExtensionV1 = {
  schemaVersion: 1,
  extensionId: "catalog",
  dependsOn: [],
  commands: [/* typed catalog/import/export/retention commands */],
  hooks: { /* only catalog lifecycle hooks required by 003 */ }
};
```

The implementation imports only node-003's shared types/registry/dispatch contract. It does not edit `extensions.ts`, `cli.ts`, or `controller.ts`. Command token paths and argument schemas are fixed in one module and validated by the real node-003 registry at registration. Required commands are:

| Command path | Required operation | Result |
| --- | --- | --- |
| `analytics catalog search` | `metadata` | Access-filtered inventory matches and safe reasons. |
| `analytics catalog rebuild` | `administration` | Durable recovery/index rebuild report with counts and bounded safe errors. |
| `analytics artifacts import` | `metadata` plus per-source metadata/sheet grants | Bounded import summary and committed import refs. |
| `analytics artifacts export` | `export` | Bounded export record linked to committed Result and answer-card revisions. |
| `analytics artifacts retention preview` | `metadata` | Exact eligible managed files and dependency/policy reasons, no deletion. |
| `analytics artifacts retention apply` | `administration` | Idempotent cleanup operation ref and completed deletion refs. |
| `analytics notebook publish` | `export` | Notebook artifact/replay status, Result refs and exploratory limitation if replay fails. |

Handlers take the authenticated principal, project/run/inquiry refs and 003-provided services from the command context. They never accept a caller path as authorization, trust worker-supplied grants, bypass node-009 persistence, or emit protected data in a `CommandResultV1` display payload. Import and retention implementations remain in `import.ts` and `retention.ts` and are called by the provider.

The `f019-t39-restricted-workbook-denial` fixture is supporting 019 catalog/access-boundary coverage: the catalog denies a lookup before FTS matching/ranking or worker-context construction and records only an access-safe denial event. Proposal T39's primary acceptance ownership remains with feature 020 per the canonical traceability audit and acceptance plan.

The provider registers `afterEvidenceCommit(context, manifestRef)` with the node-003 lifecycle dispatcher. It reconciles only outputs reachable from that committed node-009 manifest and uses stable event/commit IDs for replay. If the hook needs a durable project record, it writes through node-009 `commitProjectMemory`; a projection/index failure after the run commit remains visible and cannot be acknowledged as completed. Recovery replays the same committed history before rebuilding the index. No catalog hook edits 003-owned controller/CLI files or makes SQLite authoritative.

## Error and visibility rules

- `committed`, `replayed`, `revision_conflict` and `rejected` retain node-009 meanings.
- Access denial returns a typed safe denial without artifact title, path, digest, or row values.
- Corrupt/missing index triggers rebuild from recovered 009 project and run history; synthetic versioned card/ledger records retain their canonical bytes, refs, rationale, access scope, availability, and current validity.
- Rationale and other scoped text are grant-filtered before FTS query/ranking and before snippets or result counts are produced; denied readers get no title, reason text, or protected references.
- Malformed/partial/uncommitted output is diagnostic or absent; it cannot be indexed as a committed Result.
- A search/index failure is visible as a bounded command error and does not erase committed records.
