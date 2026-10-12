# Research: Catalog and Manage Analytical Artifacts

## Decision 1: Keep manifests authoritative and search disposable

**Decision**: Register generated artifact identity from committed run manifests and persist imports, curation, and retention events using node-009 `commitProjectMemory` / `recoverProjectMemory`. Build SQLite FTS5 only as an idempotent projection under `.dag-analytics/memory/`.

**Rationale**: The shared architecture assigns run/project CAS and recovery to 009 and catalog rebuild to 019. The project manifest, import records and curation events survive index loss; a staged/orphan file cannot become authoritative during rebuild. This also supports T28 and T37 without a second transaction protocol.

**Alternatives considered**: A separate catalog store would duplicate CAS/recovery and risk divergence. Treating SQLite rows as source-of-truth would make index loss erase project memory. Both conflict with proposal I12/I18 and the frozen interface.

## Decision 2: Preserve identity per file and group by digest separately

**Decision**: Give each committed output and each imported path its own random stable artifact ID, persisted before it is indexed. Use SHA-256 for byte identity. Store a duplicate-group reference based on project scope plus digest, but never use that group ID as an artifact ID. A changed import creates a new immutable artifact version linked to the prior import identity; a missing source changes current availability and leaves the old record intact.

**Rationale**: Two paths with identical bytes are still separate user-visible files. A digest identifies content but cannot identify the path or producer. Persisted IDs remain stable through rebuilds, and role/retention corrections do not rewrite old records.

**Alternatives considered**: Content-addressing the public artifact ID collapses duplicate paths. Deriving meaning from a filename violates I15 and SC-001. A mutable record at one path would erase changed/missing history.

## Decision 3: Parse only bounded CSV and Excel Open XML inputs

**Decision**: Support CSV and `.xlsx` in the initial importer with feature-owned, read-only parsers in `import.ts`. Reject path traversal, symlink escape, malformed archives, DTD/external XML entities, zip expansion beyond the declared budget, unsupported workbook types, and parsing beyond the 100-file, 1-GiB, or 300-second ceiling. Preflight file size before opening; stream/hash within a monotonic deadline and stop before opening further content after a limit. Do not move or copy originals.

**Rationale**: The accepted feature targets CSV and Excel workbook triage and prohibits package/toolchain edits. A bounded OOXML reader handles sheet-level grants without introducing an unowned package dependency or second runner. Limits remain controller-enforced and observable; only inspected files receive import records.

**Alternatives considered**: A generic upload or shell/office conversion command adds an unowned dependency and broadens the execution surface. Reading a whole folder before checking size/count defeats the acceptance bound. Parsing arbitrary legacy formats silently would fabricate completeness.

## Decision 4: Check grants before parsing, indexing or returning metadata

**Decision**: Require the principal and current source grant before catalog enumeration. Apply scope filters before FTS query/ranking. For workbooks, sheet access is checked before cell parsing. A metadata-only sheet grant may reveal its permitted sheet metadata but never cell values. Without metadata access, return only an access-safe unavailable pointer; do not emit a title or sheet name. Search summaries exclude raw row values by default.

**Rationale**: The access contract requires current grants at controller, catalog and viewer boundaries and access filtering before worker context. Restricted titles and values must not survive into search or context.

**Alternatives considered**: Filtering after FTS ranking leaks restricted labels through counts, snippets or title matches. A worker instruction to avoid reading unauthorized sheets is not an enforced product boundary.

## Decision 5: Use Node SQLite FTS5 as the lexical projection

**Decision**: Implement the small index adapter inside `catalog.ts` with the built-in SQLite API and an FTS5 virtual table. Store only safe catalog metadata and stable references, not source rows or large artifacts. Version the index schema; rebuild it from the recovered committed head and manifests. The active build runtime, Node v24.11.1, successfully opened an in-memory FTS5 table. Its runtime emitted Node's experimental-feature warning, so startup capability-checks SQLite/FTS5 and reports an explicit unavailable profile if absent; there is no silent JSON/in-memory substitute.

**Rationale**: SQLite FTS5 is frozen in the architecture. The repository currently has no SQLite package dependency and package manifests belong to another owner. Keeping the adapter in the graph-owned catalog module avoids adding dependencies or editing shared files.

**Alternatives considered**: A hosted/vector service is outside the initial local profile. An in-memory search index is not durable. A new package/manifest change is outside this feature's ownership. Search cannot become a source of truth regardless of backend.

## Decision 6: Enforce output and cleanup policy before file visibility

**Decision**: Require every controller-published artifact to carry a declared role, retention class, count and byte allowance, producer, exact committed references and approved output root. Default CSV/XLSX export count is zero. A requested export is created only from a committed Result version and durable answer-card reference. Reject excess output before project/run publication and keep the structured Result independent.

**Rationale**: The accepted fixture requires a zero-export default and a 100-output cap case; proposal I19 says scratch does not become a deliverable by default. The local unrestricted harness cannot be claimed to enforce write-root restrictions.

**Alternatives considered**: Saving every table as CSV makes a folder pile and does not improve answer memory. Deleting out-of-root paths during cleanup risks user data and falsely claims isolation.

## Decision 7: Treat cleanup as an idempotent durable operation

**Decision**: Persist a cleanup intent in project memory before deleting only explicitly enumerated managed `scratch` or eligible `cache` files. Check dependency links immediately before deletion. Record each deletion and reconcile pending intents against the managed root on recovery. Never automatically delete evidence, deliverables, imports or user-owned paths.

**Rationale**: An interrupted sweep must be resumable twice with the same final inventory, while its ledger must prove no evidence or outside file was removed.

**Alternatives considered**: A best-effort recursive scan can delete paths that appeared after the scan or escape through a link. Deleting before logging loses the deletion fact on crash.

## Decision 8: Require clean replay for authoritative notebook delivery

**Decision**: Keep notebooks optional and label them as `other` role with Jupyter media type. When enabled and requested, execute ordered code cells from a fresh state with exact input, program, Python, dependency-lock and environment pins using the existing 009 single-stage adapter. Validate every displayed Claim-to-Result reference. Unsupported cell features, hidden state, missing pins or a failed replay yield an exploratory notebook and zero authoritative Claims.

**Rationale**: Notebook files are editable work surfaces, not system-of-record Results. Pinning execution order and environment makes displayed values traceable without confusing notebook presence with evidence.

**Alternatives considered**: Trusting saved outputs allows hidden state and stale results to masquerade as a replay. Automatically exporting a notebook for every inquiry violates the optional profile and output limits.

## Decisions complete

No clarification remains. The accepted state vocabulary governs roles, retention (`scratch`, `cache`, `evidence`, `deliverable`), availability (`available`, `unavailable`, `redacted`) and all current-validity labels. Worker-authored suggestions retain their attribution; they cannot grant access, change role after commit, expand export policy, or declare a notebook authoritative.
