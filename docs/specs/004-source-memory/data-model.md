# Data Model: Source Memory Inquiry Integration

Feature 004 adds no authoritative record schema. It exercises the frozen
records owned by features 001, 003, 009, 014, and 015 and defines the
inspector projection needed to verify their links.

## Record and reference conventions

All committed records use `RecordRefV1` (`recordId`, positive `revision`,
and SHA-256 `digest`) from `docs/build/interfaces.md`. An input is never
identified solely by a path, table name, schema fingerprint, or `latest`
alias. Run-scoped inquiry evidence refers to committed project-scope source
and skill records; it does not copy or rewrite their history.

## Source identity and grant

The source identity is the complete registered identity used by the 014 and
015 providers:

- stable `sourceId` and exact source version/window ref;
- environment and tenant or equivalent boundary;
- schema and applicable definition versions;
- principal/role and the current `SourceGrant` ref, audience, allowed
  operations, effective period, and owner;
- source snapshot, watermark/freshness, or an explicit `unknown` value.

The principal and grant fields follow the frozen `Principal` and
`SourceGrant` definitions in `docs/build/interfaces.md`. A skill never grants
an operation. The current grant is checked again before a source read and
before the inspector returns protected metadata or bytes.

## Source guide and immutable skill version

The source guide is the feature 014 package referenced by a stable skill
record, not a feature-004 TypeScript record. Its package includes a concise
entrypoint, scoped structure/definition/grain/time references, parameterized
read recipes, assertion provenance, access metadata, validation evidence,
and lifecycle state. It has source/environment/tenant/role/audience,
schema/meaning scope, tested source version/window, and content digest.

Lifecycle values are exactly `candidate`, `active`, `stale`, and `revoked`.
Each transition or refresh points to an immutable new decision/version as
required by the owning feature. The discovery provider only creates a
candidate. After an independent bounded recipe replay, feature 004 passes the
exact candidate to feature 022's registered `skill-activation` provider via
the 003 `skills review` command. Only the configured owner and 022's recorded
validation/conflict result can activate it. Missing/failed validation,
unknown definitions, unresolved conflicts, or owner rejection leave it
`candidate`; the demo does not seed an active version. Revocation uses 022's
configured-owner `skills revoke` command. The provider records the
authority-withdrawal event; 004 does not manufacture lifecycle records.

## Assertion provenance

Each guidance assertion retains its subject and value/definition together
with one of the provenance classes required by feature 004:

| Provenance | Evidence bound to the assertion |
| --- | --- |
| `declared` | Named source/owner authority and its versioned definition ref. |
| `observed` | Source version/window and bounded metadata/profile observation. |
| `tested_on_version` | Exact source version/window, recipe and execution refs, and tested property. |
| `inferred` | Scope, evidence refs, attributed proposer, confidence/reason, and unresolved authority. |

Unresolved semantics use the canonical `unknown` sentinel. An observed sample
or successful query cannot be promoted to an owner definition. Sensitive
values, credentials, raw rows, unrestricted source comments, and untagged
study outcomes are not copied into package content or broad worker context.

## Validation and usage provenance

Validation and use are separate:

- **Recipe validation** binds the candidate recipe to exact code/parameters,
  intended grant, source version/window, bounded execution, and replay result.
- **Owner decision** records configured authority, scope, rationale, and the
  immutable version it approves or refuses. Worker interpretation cannot
  substitute for this decision.
- **Inquiry usage** binds the inquiry to the exact supplied skill `RecordRef`,
  source version, grant, reference IDs delivered, compatibility decision,
  and any exposure. Executed recipe IDs and execution refs are separate from
  the supplied-reference list.
- **Exposure** identifies any study-specific prior outcome that entered
  context; general source guidance and result memory remain separate.

The record keeps these facts distinct even if a deterministic fixture runner
observes them in one demonstration. The 022 activation decision itself is
identified by its exact immutable project-memory `RecordRefV1`; it is never
reconstructed from a successful command exit or worker recommendation.

## Compatibility and invalidation

Feature 015 owns the sole source-skill compatibility predicate. Its
assessment covers source identity, environment/tenant, current grant and
audience, lifecycle state, schema and key/grain meaning, units/timezone,
category/metric definitions, exact source version, watermark/freshness, and
recipe privileges. Missing required facts remain unresolved; known semantic
conflicts cannot qualify as compatible merely because schemas match.

An invalidation event binds the changed fact, source version, affected
assertion/recipe refs, lifecycle result (`stale` for changed assumptions or
`revoked` for withdrawn authority), dependent inquiry refs, and reason. An
unused column affects no unrelated guidance. A failed join on a later window
invalidates the tested join/recipe facts whose evidence it contradicts, not
all source knowledge.

## Locked inquiry and answer inspector projection

When an inquiry is admitted, its input manifest pins the source version and
any supplied skill version. Later drift or revocation changes current
validity but not the historical lock. Feature 004 reads the answer and claim
trace through the existing feature 011 HTTP routes:
`GET /api/analytics/inquiries/{inquiryId}/answer` and
`GET /api/analytics/claims/{claimId}/trace`, also opened by the frozen
`answer show --open` and `trace --claim` commands. The acceptance fixture
follows the returned committed evidence links to the source, skill, 022
validation/authority decision, and 015 usage records, and checks their exact
IDs/revisions/digests under the current viewer grant. It asserts 011's
existing generic `linkedProvenance` relationships through the existing
responses; 004 adds no viewer route or feature-specific DTO. The inspector
renders:

- exact source/skill IDs, revisions, digests, and lifecycle/current validity;
- environment, tenant, role, audience, definitions, and grant references;
- each assertion's provenance and its validation/source-window refs;
- references supplied versus recipes actually executed;
- compatibility/invalidation or authority decision with actor, reason, and
  evidence refs;
- outcome exposure status and any disclosed local workflow boundary.

If current guidance is incompatible, stale, revoked, over-privileged, or
unknown, the inquiry cannot silently replace its locked input. It reports the
unresolved meaning or a canonical non-answer/needs-input outcome as the
controller's evidence and policy rules require. A new source or refreshed
guide requires an explicitly admitted inquiry binding.
