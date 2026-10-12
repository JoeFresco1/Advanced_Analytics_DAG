# Data Model: Source Skill Discovery

These feature records are immutable payloads committed through the shared project-memory mechanism. Their machine skill lifecycle is the canonical candidate state; this feature does not define another lifecycle vocabulary.

## Source scope

One discovery scope binds:

- Stable configured source ID and environment; tenant or project identifier when configured.
- Current principal and project role from the authenticated context.
- SourceGrant reference, permitted operations, and source audience.
- Schema scope and meaning scope as distinct values.
- A source version reference or explicit unknown value, discovery time, and snapshot guarantee.
- Adapter access guarantee: read-only SQLite connection plus whether host-level source isolation is enforced or workflow-only.

The configured source ID resolves to a local database only after the grant check. Generated artifacts omit absolute paths and connection details. Two same-named tables from different source environments have different scopes.

## Discovery profile

A discovery profile records the exact visible schema objects, permitted column metadata, bounded sample window, aggregate summaries, query/parameter digests, query budget, and any stop reason. It retains no raw source row or sample value. Counts derived from a bounded profile are identified as sample observations and never described as population-wide totals.

Bounds selected for this implementation are 100 visible tables, 200 columns per table, 20 profiled tables, 1,000 sample rows per table, and 30 seconds per discovery. Uninspected objects and unavailable byte/query-cost telemetry remain visible.

## Source assertion and provenance

Each assertion records:

- Stable assertion ID, subject scope, predicate, value, and supporting evidence references.
- Provenance kind: declared, observed, tested_on_version, or inferred.
- For tested assertions, exact source version/window, recipe ID, execution reference, and validation time.
- Named authority reference when a meaning is owner-defined.
- Conflict references to both the source assertion and the owner definition when they disagree.

An unavailable meaning uses the canonical unknown sentinel. Schema observations and inferred joins cannot silently fill it. Conflicting definitions remain linked; a candidate package cannot promote the conflicting value as authority.

## Read recipe

A recipe contains a stable recipe ID, parameter schema, one approved read-only SQL statement, declared SourceGrant operation and audience, maximum result shape, query timeout/budget, tested source scope/version, execution evidence reference, and a bounded validation summary. Generated first-release recipes return aggregates only. SQL values use bound parameters; identifiers come from discovered schema objects and are quoted as identifiers. Recipes requiring row, export, administration, broader audience, or otherwise unavailable permission are not executed under a weaker grant.

## Source skill package

Each immutable package version contains:

- Stable skill ID, version/revision, content digest, created time, and canonical lifecycle state candidate.
- Source scope and access metadata.
- Short SKILL.md entrypoint.
- On-demand schema, definitions, joins/grain, and time/freshness references.
- Parameterized Python examples, tested recipe references, claim provenance, and validation evidence.
- Unknown meanings, authority references, conflicts, bounds, and invalidation conditions.

The manifest and every referenced file share the same audience scope. New discovery or correction writes a new package version; it never rewrites the package pinned by an earlier inquiry. This package contains no credentials, raw sensitive rows, or study-specific outcome summaries.

## Persistence and projections

source-skills.ts submits an immutable skill record and package artifact references through 009 commitProjectMemory. 009 supplies commit ID idempotency, expected-revision CAS, access validation, and recovery. 014 projects its own source-skill record refs from the committed 009 project head after 001 access filtering; 019 does not supply source-skill lookup. Neither the package manifest nor a search index is an alternate authority for project history.

## Ordinary-ask orientation presence check

The production `orient` hook reads only currently accessible active source-skill record refs from committed project memory through 014's access-filtered projection over the committed 009 project head. It compares the exact configured source identity, environment, and tenant. It does not treat matching table names or schema fingerprints as identity and does not evaluate compatibility.

- With zero matching active refs, the hook may run cold discovery only after current metadata and aggregate grants and remaining discovery budget are verified. It uses the same implementation as `source discover`, commits a candidate, and returns only its access-filtered reference and scope metadata.
- With one or more matching active refs, the hook returns safe refs only. It does not rediscover, publish a candidate, expose candidate content, or call a 003/015 compatibility service. The later 015 orientation hook owns compatibility assessment.
- Stale or revoked records are not active. Their absence does not let 014 infer revocation or authority; a cold read still requires current grants and budget.

These are branch rules for the hook, not a new skill lifecycle or compatibility state vocabulary.
