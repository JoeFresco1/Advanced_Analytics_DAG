# Implementation Plan: Catalog and Manage Analytical Artifacts

**Branch**: `019-catalog-analytical-artifacts` | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Accepted feature specification in `specs/019-catalog-analytical-artifacts/spec.md`.

## Summary

Feature 019 gives every committed output a stable, provenance-bound catalog record and makes project search a rebuildable view of committed run/project histories, including imports, curation, answer-card projections, and versioned ledger attempt, rationale, and match-assessment projections. The implementation owns the artifact catalog, bounded CSV/Excel workbook import, scoped search/index rebuilding, output admission, scratch retention, and optional notebook replay metadata. It exports `analyticsExtensionV1` from `catalog.ts` for node-003's typed CLI/controller registry and lifecycle dispatcher. The provider handles catalog/import/export/retention/notebook commands and reconciles committed artifacts through `afterEvidenceCommit`; import, curation, and retention records use node-009 `commitProjectMemory` and `recoverProjectMemory`. The feature does not add another CAS or publication engine. Imported files remain separate from answer cards and keep unknown provenance unless committed evidence establishes a link.

## Technical Context

**Language/Version**: TypeScript, ES2022 modules, Node.js active build runtime v24.11.1. The existing package declares `node >=20`; the catalog adapter checks that FTS5 support is available at runtime and fails closed if it is not.

**Primary Dependencies**: Existing node-003 `AnalyticsExtensionV1` types and context; node-009 committed run/project manifests and project-memory CAS/recovery; node-001 principal, current-grant, approved-root and stage boundaries; SQLite FTS5 as required by the frozen architecture, accessed through Node's built-in SQLite adapter available in the active Node 24 runtime. No package or lockfile changes are part of this feature.

**Storage**: Authoritative imported-artifact, curation and retention event records through node-009 project memory; output artifacts remain in node-009 committed run manifests or approved managed roots; imported bytes stay at their original locations by default. `catalog.sqlite` is an idempotently rebuildable FTS5 projection under `.dag-analytics/memory/`.

**Testing**: Feature-owned synthetic/local tests in `src/analytics/acceptance/019.test.ts`; focused execution with `pnpm exec tsx --test src/analytics/acceptance/019.test.ts`; final feature and repository checks with `pnpm build` and `pnpm test`. No production data or external service is used.

**Target Platform**: Existing local TypeScript CLI/controller and project sidecar. SQLite index is project-local. Artifact access goes through principal/grant checks and approved roots.

**Project Type**: TypeScript analytics CLI/controller extension and local catalog adapter.

**Performance Goals**: Enforce the accepted import limits exactly: at most 100 file contents inspected, at most 1 GiB of input bytes, and at most 300 seconds per import. The 99-file synthetic fixture completes within those limits; the 101st file and files beyond a byte/time limit are not read or inventoried as inspected. Output admission enforces the caller's declared count and byte bounds before project publication.

**Constraints**: Preserve the exact accepted FR/SC/Edge Case contract and proposal T28–T30/T37/T43–T47. Retain restricted-workbook denial as supplemental 019 access-boundary coverage for 020-owned proposal T39; do not claim primary T39 ownership. Filter access before ranking or adding material to worker context. No raw row values in searchable summaries by default. Never infer execution, result, or answer-card provenance from a filename. Keep staged/failed/imported/committed states separate. Only controller-owned roots are eligible for automatic cleanup. Use synthetic/local fixtures. Do not alter `dag.run.json`, shared interfaces/graph, package manifests, accepted specs/checklists, CLI/controller-owned files, or any other feature.

## Constitution Check

### Before design

- **I15 / IV**: The catalog gives each committed output an identity, role and source/provenance link, with an explicit unknown-origin state for untraceable imports.
- **I18 / IV**: Search is only a projection; committed manifests, import records and curation history remain authoritative and can rebuild it.
- **I19 / V**: Output count, byte and retention limits are checked before publication; scratch, evidence, cache and deliverable remain distinct.
- **I13 / V**: A declared output path is not described as an enforced write boundary. The unrestricted profile inventories and warns about unmanaged writes.
- **I12 / I**: Catalog/import/curation commits use node-009's single project-memory writer and recovery; no second CAS protocol is introduced.
- **I14 / VI**: The 003 typed command/controller extension produces catalog and import results; users inspect scoped search and artifact links through existing catalog/viewer paths; acceptance injects crash, corruption, access and cleanup failures.

**Gate**: Pass. These are consistent with the accepted feature and shared interfaces. No policy, threshold, label, state vocabulary or source ownership is changed.

### After design

The design retains the same boundaries. `ArtifactRecordV1` is immutable and linked by stable refs; index loss cannot create an artifact or promote staged content. Search filters by the current grant before FTS matching. The importer is count-, byte-, time-, root- and sheet-bounded; metadata-only access never returns cells. Export and retention operations use explicit policy and durable records, and notebook publication requires clean replay against pinned inputs. The optional notebook path cannot make its output the only Result record.

**Gate**: Pass. No constitution exception is needed.

## Phase 0: Research and design decisions

See [research.md](research.md). The frozen local SQLite/FTS5 architecture, artifact roles and retention states, access boundaries, and node-009 persistence signatures settle the implementation choices. The importer handles CSV and Excel Open XML workbooks using a bounded read-only parser in the feature-owned `import.ts`; it does not add a package dependency. Unsupported, corrupt, protected or over-limit inputs produce access-safe import status without fabricated records. No unresolved clarification remains.

## Phase 1: Domain design and contracts

See [data-model.md](data-model.md) and [contracts/](contracts/). The catalog is a stable-ID projection over committed manifests and durable project records. Import records identify each selected original independently of digest grouping. Exports and notebooks are committed deliverables only after declared policy, exact Result/answer bindings, and replay conditions pass. Retention records make partial cleanup recoverable.

See [quickstart.md](quickstart.md) for synthetic 99-file import/search, limit enforcement, export, retention, workbook-grant and notebook-replay scenarios.

## Production path and trust boundaries

1. Node 003's typed CLI/controller provider resolves the authenticated principal and required project/source grant before invoking catalog commands. The catalog applies the current grant again before exposing metadata or returning bytes.
2. Node 009 owns run and project publication. Committed run manifests establish generated output identity and consumer refs. Project-scoped import, curation, and retention records call `commitProjectMemory`; `appendCurationEventV1(CatalogCurationEventV1)` records immutable actor, target artifact, permitted action, access scope, and exact evidence refs with stable event/commit IDs. Index recovery begins from `recoverProjectMemory` and committed run manifests; 019 adds no CAS or publication mechanism.
3. `catalog.ts` reads records reachable from committed run/project history only, maintains the rebuildable SQLite FTS5 index, and exports the typed `analyticsExtensionV1` provider. Task T009 explicitly owns the `analytics artifacts import` handler in `catalog.ts`, which passes authenticated node-003 context and current grants to the bounded importer in `import.ts`. Node 003 dispatches the exact catalog/import/export/retention/notebook command paths and `afterEvidenceCommit` hook; the feature-owned acceptance path exercises those calls through the real registry/dispatcher without editing 003 files. It does not create answer cards or decide that an output is a finding. Features 020/023 remain owners of answer ranking and ledger semantics.
4. `import.ts` imports selected CSV and `.xlsx` originals read-only, observes metadata and permitted profiles under a 100-file/1-GiB/300-second bound, honors sheet grants, preserves unknown origin, and groups byte-identical files while retaining separate path identities.
5. `retention.ts` validates declared outputs before commit, binds user-authorized exports to committed Result versions and answer cards, verifies optional notebook replay metadata, and cleans only eligible managed scratch/cache entries after durable intent.
6. The existing artifact viewer resolves IDs through committed manifests and current grants. It serves declared bytes under approved roots; metadata readers receive neither raw rows nor restricted paths/titles.

The worker shell can exceed product-enforced root restrictions in the local profile. Before/after inventory and an unmanaged-output warning report what was observed; neither is described as filesystem isolation.

## Failure and recovery design

- A crash between run evidence commit and catalog projection is repaired by replaying `afterEvidenceCommit` from the committed run manifest and indexed once. An uncommitted staged/orphan artifact remains absent; a failed required hook leaves completion unacknowledged.
- Project import, curation, card and ledger projection inputs are durable before index projection. The T004 `f019-curation-commit-rebuild` fixture commits a curation event through 009, recovers project memory, deletes or corrupts the index, and verifies that the curation event and artifact/evidence refs return without changing canonical bytes or history. A lost/corrupt SQLite file is rebuilt idempotently from the full 009 recovered project/run history. Versioned ledger attempt, rationale and match-assessment inputs are read through `CatalogProjectionInputV1`; 019 does not import or implement 023 ledger semantics. Rebuild preserves canonical record bytes, refs, access scope, availability, current validity and usage links, and only the SQLite projection changes.
- Count/byte/time violations stop before inspecting further file contents or committing an over-limit artifact. The bounded stop report records observed counts, consumed bytes/time and the limit reached; it does not invent records for unread files.
- Grant denial occurs before a sheet's cell data, title or path can enter profiles, search results or worker context. Metadata-only grants may reveal only permitted metadata and never cell values. The `f019-t39-restricted-workbook-denial` fixture is supporting 019 catalog/access coverage; 020 retains primary ownership of proposal case T39.
- Cleanup writes its bounded intent through 009 before deletion, only removes listed managed scratch or eligible cache with no retained dependency, and records each completed deletion. Recovery reconciles the intent with remaining managed files and is idempotent.
- A notebook is an authoritative deliverable only after a fresh-state run matches its pinned inputs/environment and every displayed claim resolves to a committed Result ID. Hidden-state dependence, failed replay, unsupported execution features or missing metadata leave it exploratory and support zero Claims.

## Compatibility and migration effects

The run manifest remains the authority for committed run artifacts. The project manifest remains the authority for import, curation and retention records. Existing project sidecars gain versioned records through node 009; no `dag.run.json` schema or neutral runner behavior changes. SQLite schema is disposable/rebuildable and carries a schema version; a rebuild never rewrites artifact IDs or history. Original imported files are not copied or moved by default. Access, role, retention and availability changes are append-only record revisions/events. No new answer-card store is added.

## Project structure

### Planning artifacts

```text
specs/019-catalog-analytical-artifacts/
├── plan.md
├── research.md
├── data-model.md
├── contracts/
│   ├── catalog-v1.md
│   └── import-delivery-v1.md
├── quickstart.md
├── tasks.md
└── execution.json
```

### Owned implementation outputs

```text
src/analytics/
├── catalog.ts                    # Artifact identity, registration, FTS5, safe search, typed provider
├── import.ts                     # Bounded CSV/XLSX import and sheet-grant enforcement
├── retention.ts                  # Output admission, export/notebook validation, cleanup recovery
└── acceptance/
    └── 019.test.ts               # Synthetic end-to-end acceptance matrix

specs/019-catalog-analytical-artifacts/verification.md  # Written by final unit after actual checks
```

**Structure Decision**: Use only the four implementation paths assigned by `docs/build/graph.json`. The plan and units consume node 003's frozen provider types, node 009's project-memory commit/recovery and node 001's principals/approved-root boundaries. The final unit owns `verification.md`; this planning stage does not claim test evidence.

## Complexity Tracking

No constitution violations or extra subsystem. SQLite is only a projection; file identity and project history remain in the existing append-only manifest protocol.
