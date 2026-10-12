# Research: Source Skill Discovery

**Status**: Decisions resolved from the accepted specification and frozen shared interfaces.

## Decision 1: Use the existing single-stage executor for SQLite access

The TypeScript facade owns source identity, grant checks, discovery bounds, result validation, and package generation. SQLite metadata queries and generated Python recipes run through the existing 009 executeStageV1 path, using a fixed bridge and Python's standard-library sqlite3 module. The bridge opens a read-only SQLite URI, enables query-only mode, and accepts the selected source and parameters as structured inputs. The SQL statement and bridge code are fixed or generated from the source schema; source comments and labels never become executable program text.

**Rationale**: The shared architecture requires one neutral process executor and the repository supports Node >=20 without a SQLite package. Python is already the locked computation runtime. This avoids adding a second process manager, a new package dependency, or a Node-only SQLite runtime requirement.

**Alternatives considered**: Node's built-in SQLite API would narrow runtime support beyond the package's Node >=20 contract; adding a native package would require the shared package manifest/lock owner; a standalone sqlite3 executable is not a declared repository dependency.

## Decision 2: Use explicit bounded profiling defaults

One discovery admits at most 100 visible tables, 200 columns per table, 20 profiled tables, and a 1,000-row profile sample per table. Its admitted query stage has a 30-second discovery budget. Hitting a cap returns a partial discovery with the exact bound and unfinished scope; it does not silently continue with unrestricted reads. Profiling stores counts and aggregate summaries only, never sample rows or raw field values. Source-byte accounting is reported unavailable unless the adapter can measure it.

**Rationale**: These values provide deterministic limits for the first local fixture without changing any accepted evaluation threshold. The per-query sample ceiling and SQLite progress callback bound work; the neutral stage timeout is the outer stop.

**Alternatives considered**: Full-table scans, unrestricted sampling, and increasing bounds until a result looks complete are excluded because they do not provide bounded source access.

## Decision 3: Treat source identity, environment, role, and audience as required scope

The source identity is a configured opaque source ID resolved only after the current principal has a matching metadata grant. The candidate manifest records source environment, tenant/project scope where applicable, granted role, audience, schema scope, meaning scope, exact source version when available, and access guarantee. A database path or connection details do not appear in generated files. Same-named tables in different environments produce separate candidates.

**Rationale**: Schema equality does not establish equal meaning or permission. The access contract makes missing, expired, revoked, or too-narrow grants fail closed.

**Alternatives considered**: Table name, filename, or schema fingerprint alone cannot identify source meaning and are not accepted as package scope.

## Decision 4: Preserve unknown meaning and claim-level provenance

Each source assertion has a provenance kind of declared, observed, tested_on_version, or inferred, plus evidence references. Tested assertions name the source version/window and recipe execution. A meaning not declared by an authoritative source remains the canonical unknown sentinel. A source/owner disagreement retains both assertion references and the named authority; it does not replace the owner definition or become active guidance.

**Rationale**: The feature spec requires evidence and authority to remain separately attributable. Schema and distribution observations do not establish business meaning.

**Alternatives considered**: Inferring a denominator, category, unit, join rule, or time definition from column names or sample distributions would violate FR-007.

## Decision 5: Publish an immutable candidate through project memory

The package contains a short SKILL.md, scoped on-demand references, a manifest, parameterized read-only examples, validation evidence, provenance, and access metadata. It is written as a new immutable version and committed with 009 commitProjectMemory using stable commit identity and expected revision. The source-discovery extension retains the frozen dependsOn catalog for extension order, while 014 reads its own committed source-skill refs from the 009 project head and applies 001 filterAccessible before inspecting active status. Its ordinary-ask `orient` hook checks only for currently accessible active source-skill refs matching exact source identity, environment, and tenant. If none exist, it verifies current metadata/aggregate grants and remaining budget, then calls the same bounded discovery function as `source discover`; it commits only a candidate and returns safe candidate refs/scope. If any active refs exist, it returns safe refs only and defers compatibility to the later 015 orientation hook without rediscovery. Stale/revoked records do not count as active, but 014 does not infer revoked authority or bypass current access. It calls no 003 or 015 compatibility service. This feature creates only candidate guidance; 022 owns activation.

**Rationale**: Project memory is append-only under 009, while 014 owns the access-filtered source-skill presence projection over the committed 009 head; 019 owns artifact catalog indexing and is not asked to return skill refs. A presence-only check lets 014 perform safe cold discovery before 015 exists while preserving 015 as the sole compatibility judge when active refs exist. A skill package is durable guidance distinct from answer cards, claims, and analysis outcomes.

**Alternatives considered**: Editing a prior package, writing a local standalone registry, creating a second CAS mechanism, calling compatibility from 014, or marking a candidate active are outside this feature contract.

## Decision 6: Keep all source text inert and omit study outcomes

Text obtained from table comments, schema SQL, labels, examples, or user-supplied metadata is parsed as data and is never concatenated into commands, code, privileged prompts, or policy fields. Raw comments and examples are omitted from generated guidance by default; if a later review needs to inspect them, the package can carry only an audience-scoped evidence pointer/digest. Owner-authorized definitions enter as separate declared assertions with a named authority. Only allowlisted schema identifiers are quoted for SQL; all values are bound parameters. The package excludes credentials, raw rows, and study-specific outcome summaries, including evaluation results.

**Rationale**: The package persists across worker invocations and therefore must not turn source content into privileged instructions or leak prior outcome exposure.

**Alternatives considered**: Copying raw metadata wholesale into SKILL.md or preserving an evaluation summary without exposure provenance is rejected.

## Open questions

None block planning. Missing source versions and source snapshot guarantees are represented as unknown or unavailable facts instead of assumptions.
