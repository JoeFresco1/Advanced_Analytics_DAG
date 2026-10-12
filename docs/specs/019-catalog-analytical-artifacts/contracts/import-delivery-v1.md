# Import, Output, Notebook and Retention Contract V1

## Bounded import

The initial owner-approved import profile is immutable and records `profileId`, revision and its exact ceilings:

| Limit | Maximum |
| --- | ---: |
| File contents inspected per import | 100 |
| Total source bytes read per import | 1 GiB |
| Elapsed import time | 300 seconds |

A request identifies a project, authenticated principal, approved source-root ref, current grant refs, import profile/revision, import ID and optional explicitly selected relative paths. It does not accept an arbitrary worker path as authority. The importer canonicalizes the selected root and child paths, rejects traversal and symlink escape, and never moves or copies originals by default.

The importer checks count and declared file size before opening file contents; hashes and parses incrementally with byte/deadline checks. When a ceiling is reached it stops before opening further content and records only inspected files plus a bounded stop report. A file whose content changes during the scan is re-statted and its digest/version is not presented as one stable observation. Exceeding 100 files, 1 GiB or 300 seconds does not silently raise the bound; larger imports require a distinct owner-authorized profile revision and new import run.

Supported initial formats are CSV and Excel Open XML `.xlsx`. The parser rejects malformed/over-expanded archives, path escape within the archive, DTD/external entities, unsupported/encrypted workbook forms, and unsupported formats without inventing a parsed profile. It performs no formula recalculation or macro execution. Sheet names and cell content are independently protected by workbook grants.

For every inspected file, the importer records its own immutable path-scoped ID, byte digest, size, media type, permitted schema/profile, import time, availability and explicit unknown execution provenance. Same-byte files at distinct paths remain separate records and share a duplicate-group reference. Reimporting a changed path creates a new immutable artifact version linked to its previous import identity and a durable source-change event; dependent current projections become `reassessment_required`. Missing files become unavailable on rescan without rewriting historical provenance; availability and current validity remain separate. A larger import uses a separately owner-authorized, versioned bounded profile and a new import run.

## Workbook access

Before reading sheet content, the controller checks the current sheet grant for that workbook and principal. A metadata grant allows only the explicitly permitted sheet names and safe workbook metadata. A sheet grant without metadata permission returns only an access-safe unavailable pointer; it cannot disclose the workbook title or sheet name. A cell/profile grant is required before permitted cell values are profiled. Unauthorized sheet XML/cell values are never put in import profiles, FTS fields, result payloads or worker context. The implementation does not trust workbook titles, comments, formula strings or metadata as instructions. The handler-path grant check is supporting coverage for proposal T39; feature 020 retains primary T39 acceptance ownership. 019 separately owns its catalog denial boundary, which filters access before FTS ranking and context construction.

## Bounded controller publication

Every generated artifact is validated against a committed producer record and a declaration before publication. The declaration contains stable artifact ID, canonical role, retention class, intended root, media type and count/byte ceilings. The initial acceptance profile tests a 100-artifact controller publication limit. Unknown/undeclared roles, absent retention class, output 101, wrong root and byte overflow are rejected before the commit; previously committed evidence remains available.

Default CSV and `.xlsx` export allowance is zero. A user request or preconfigured deliverable policy can admit a bounded export. Each accepted export is generated from an exact committed Result revision and linked to a durable answer-card revision. The Result remains accessible without the file. Over-budget output stays an uncommitted diagnostic; the controller retains the structured Result/answer and reports the violation. For an unrestricted local worker, before/after inventory flags observed unmanaged files and says write-root enforcement is not guaranteed.

## Notebook replay

Notebook delivery is optional and enabled only by an explicit profile plus user request. A candidate notebook binds:

- exact input artifact/source version refs;
- program/code, Python, dependency-lock and environment refs;
- ordered cell IDs and code digests;
- a fresh-state clean replay result under node-009 `executeStageV1`;
- the declared output/Result refs and a Result ID for each displayed Claim;
- export grant, output count/byte reservation and notebook artifact digest.

A clean replay begins without saved execution state and follows cell order against the pinned input and environment. The controller compares produced Result bindings/values under the declared comparison rule. Missing links, stale inputs/environment, hidden-state dependence, unsupported cells or a failed rerun leave the notebook labeled `exploratory`; it supports zero authoritative Claims. A saved notebook output alone never becomes a Result.

## Managed retention and crash recovery

Automatic cleanup can select only `scratch` and policy-eligible `cache` entries whose roots are managed and whose dependency graph does not protect evidence/Result/Claim/Report links. `evidence` and `deliverable` require their explicit retention policy and dependency check. Imported files and user-owned paths are never routine deletion targets.

`preview` returns exact managed candidates and reasons without mutation. `apply` first commits an append-only cleanup intent via node-009 project memory, then removes only enumerated safe candidates and durably records each completed deletion. On crash, recovery uses `recoverProjectMemory`, checks each candidate under the managed root, resumes the same intent, and records the actual final inventory. Re-running recovery is idempotent. It never recursively follows an untrusted path, deletes an out-of-root file, or removes a newly linked evidence artifact.
