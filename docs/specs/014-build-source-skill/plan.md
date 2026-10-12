# Implementation Plan: Build a Source Skill

**Branch**: 014-build-source-skill | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Accepted feature specification in specs/014-build-source-skill/spec.md

## Summary

Build a single SQLite source-discovery path that checks the caller's current source grant before reading, profiles metadata and bounded aggregates, tests parameterized read-only Python recipes, and emits an immutable source-skill candidate. The package separates source identity and access scope from schema and meaning claims, preserves each claim's declared/observed/tested/inferred provenance, and leaves inferred definitions unknown until a named authority resolves them. Register the producer through the 003 extension registry; commit the candidate through 009 project memory and let 019 index any committed artifact references in its own scope. During ordinary `ask`, the production `orient` hook reads the currently accessible active source-skill record refs for the exact source identity, environment, and tenant from committed project memory through 014's access-filtered projection over the committed 009 project head. If zero active refs exist, it checks current metadata and aggregate grants plus remaining discovery budget, then calls the same bounded discovery implementation as `source discover`; it commits only a candidate and returns its access-filtered reference and scope metadata. If one or more active refs exist, it returns safe refs only and does not rediscover or assess compatibility; the later 015 orientation hook owns that judgment. In both paths 014 never supplies candidate content as active guidance or activates it. Stale or revoked records do not count as active, but 014 does not infer revoked authority or bypass current access checks. An explicit `source discover` call remains available for refresh. Source text remains untrusted data.

## Technical Context

**Language/Version**: TypeScript 5.6 on the repository's Node.js >=20 runtime; Python examples use the repository's uv-managed interpreter.

**Primary Dependencies**: Existing analytics extension registry from 003; 009 stage execution and project-memory commit/recovery; the committed 009 project manifest and 001 filterAccessible for 014-owned source-skill refs; Python standard-library sqlite3. 014 uses no 003 or 015 compatibility service and introduces no shared context field. No new package, CLI tool, manifest, or feature-graph dependency is introduced.

**Storage**: Read-only local SQLite source; immutable candidate package and source-skill record under project memory at .dag-analytics/sources/<source-id>/skills/<version>/; committed project records use 009's project-scoped CAS and append-only history.

**Testing**: Node test runner through the existing pnpm test command, with synthetic SQLite databases created in src/analytics/acceptance/014.test.ts. The tests execute discovery and generated Python recipes against that local fixture.

**Target Platform**: Local analytics CLI with the existing neutral stage executor; no remote source connector or distributed discovery.

**Project Type**: TypeScript analytics CLI/controller and project-memory library with Python source examples.

**Performance Goals**: One SQLite adapter; at most 100 visible tables, 200 columns per table, 20 profiled tables, 1,000 sampled source rows per table, and 30 seconds for one discovery profile. Stop at the first reached cap and retain the limitation. A progress callback and the admitted stage timeout bound SQLite work; exact bytes scanned remain unavailable unless the host measures them.

**Constraints**: Require metadata grant before schema reads and aggregate grant before profiling. Open SQLite using a read-only URI, enable query-only mode, admit one SELECT/EXPLAIN statement per recipe, bind values instead of interpolating them, and bound query work and returned summaries. Do not persist row values, credentials, local connection paths, or study-specific outcomes. A broad local shell remains a workflow access boundary unless the host enforces stronger isolation.

**Scale/Scope**: One registered SQLite source and one intended audience/role per candidate version. Table names are not sufficient identity: source ID, environment, tenant or project scope, role, audience, schema/meaning scope, and source version/window are recorded separately.

## Constitution Check

### Pre-design gate

- The feature keeps execution, evidence, meaning, authority, access, and skill lifecycle separately attributable.
- A successful recipe process is recorded as a tested recipe only for its exact source version/window, code, parameters, grant, and execution. It does not activate the skill or establish business meaning.
- Unknown source meaning remains unknown; conflicting source and owner definitions remain linked and block promotion of the conflicting assertion.
- Source grants are checked before reads. Read-only SQLite settings are enforced at the adapter, while any broader worker-shell guarantee is described accurately.
- Candidate content is immutable, audience-scoped, and persisted through 009. No second project-memory writer or CAS protocol is introduced.
- The source-discovery producer is registered through 003 and depends on 009 for committed source-skill refs and 001 for access filtering; 019 remains an extension-order/catalog prerequisite. Its production `orient` hook checks for currently accessible active refs matching the exact source identity/environment/tenant. With zero refs, it checks current metadata and aggregate grants and remaining budget before invoking the same bounded discovery function as `source discover`; it commits only candidate metadata and returns safe candidate refs/scope. With one or more active refs, it returns safe refs only and does no discovery or compatibility judgment. The later 015 orientation hook alone assesses compatibility. 014 calls no 003 or 015 compatibility service and adds no shared context field. Injected source text is included as data only.
- Active-reference presence is not a compatibility decision. Stale or revoked records are not active, but 014 does not infer why a ref is absent or revoked; every read still requires the current grant and budget.
- Synthetic/local fixtures cover successful discovery and a deliberately contaminated source. No accepted spec, threshold, checklist, or reference answer changes.

**Gate result**: Pass. No constitution exception is required.

### Post-design gate

The proposed SQLite adapter, candidate lifecycle, provenance, access filtering, project-memory publication, extension registration, and failure fixture preserve the pre-design constraints. Discovery creates only candidates. Existing provider order is respected through source-discovery depending on catalog. Acceptance invokes the registered `source discover` command through the 003 extension registry and checks its candidate output and denied-grant stop. It separately exercises the production orientation hook on both the zero-active-ref cold path and the one-or-more-active-ref deferral path, then verifies denied-grant and exhausted-budget stops independently of the `source discover` command. 014 owns only the exact-scope active-ref presence check; 015's later orientation hook owns compatibility. The 014 slice does not own activation or 003/019/009 shared files.

**Gate result**: Pass.

## Project Structure

### Documentation (this feature)

    specs/014-build-source-skill/
    ├── plan.md
    ├── research.md
    ├── data-model.md
    ├── contracts/
    │   └── source-discovery.md
    ├── quickstart.md
    ├── tasks.md
    ├── execution.json
    └── verification.md

### Source Code (repository root)

    src/analytics/
    ├── source-discovery.ts       # SQLite adapter, bounded discovery, extension provider
    ├── source-skills.ts          # immutable candidate package, provenance, scope filtering
    └── acceptance/
        └── 014.test.ts           # synthetic end-to-end and contaminated-source fixtures

Generated package files are project-memory artifacts, not checked-in implementation modules:

    .dag-analytics/sources/<source-id>/skills/<version>/
    ├── SKILL.md
    ├── manifest.json
    ├── references/
    │   ├── schema.md
    │   ├── definitions.md
    │   ├── joins-and-grain.md
    │   └── time-and-freshness.md
    ├── examples/query.py
    └── checks/manifest.json

**Structure Decision**: Keep behavior in the three files owned by graph node 014. The generated package is immutable project memory, not a second source registry. The provider is exported from source-discovery.ts and declares its frozen dependency on the 019 catalog provider. All durable source-skill records use 009 commitProjectMemory/recoverProjectMemory.

## Complexity Tracking

No constitution violations or additional project layers are proposed.
