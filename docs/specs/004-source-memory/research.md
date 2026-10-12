# Research: Source Memory Integration

**Scope**: Resolve integration choices for feature 004 from the accepted
feature spec and frozen local contracts. No external research was needed.

## Decisions

### Use the normal inquiry and extension paths

**Decision**: Drive both questions through the feature 003
`dag analytics ask` path. On a cold source, feature 014's registered
`orient` hook calls the same grant- and budget-bounded discovery
implementation as the `source discover` command, commits a candidate, and
returns only its safe reference to orientation. The later ordinary inquiry
reaches feature 015's `source-reuse` `orient` hook through the fixed
`AnalyticsExtensionV1` registry and dependency order. Open answers and traces
through the existing answer/trace paths and inspect their generic
linked-provenance projection.

**Rationale**: Feature 004 is an integration milestone; direct calls to
discovery or compatibility helpers alone would not show a real consumer.
Shared interfaces already freeze the CLI, extension hooks, provider names,
and ownership of `controller.ts`, `cli.ts`, and the viewer.

**Alternatives considered**: Add a 004 provider or edit 003's registry and
controller; rejected because 003 owns those shared files. Run discovery
outside the inquiry; rejected for the primary demonstration because the
first inquiry must exercise normal orientation.

### Keep source discovery, validation, authority, activation, and reuse distinct

**Decision**: Feature 014 remains candidate-only. Its bounded independent
recipe replay and assertion evidence are inputs to the real feature 022
`skill-activation` provider. After the first live inquiry creates the 014
candidate and the replay is committed independently, the demo invokes 022's
registered `skills review` command through 003's normal typed command
registry with
the exact candidate, configured owner, decision, rationale, and expected
project revision. Only the provider can record approval and create an
immutable active version after required validation and conflict checks pass.
The live worker never approves or promotes its own candidate. Failed or
missing replay, unknown meanings, unresolved conflicts, and owner rejection
remain `candidate`; the fixture never seeds active records. The revocation
case uses 022's `skills revoke` command under the configured owner, then
verifies that new 015 orientation excludes the revoked version while earlier
inquiries keep their exact pins.

**Rationale**: Canonical lifecycle rules distinguish `candidate`, `active`,
`stale`, and `revoked`. The 014 discovery contract says discovery never
transitions a package out of `candidate`; the access/judgment contract assigns
authority to the configured owner. The 022 provider contract owns production
source-skill activation and revocation, while the 015 spec assigns one
compatibility predicate and preserves historical pins. Feature 022 is an
explicit graph prerequisite for 004, so this slice exercises its provider
instead of simulating lifecycle records.

**Alternatives considered**: Let a successful command or model opinion
activate the candidate; rejected because execution is not authority and
worker-authored checks alone are insufficient. Seed an active or revoked
record directly; rejected because it would bypass 022's provider and fail to
exercise the actual dependency. Implement another activation/revocation path
in 004; rejected because it would duplicate feature ownership.

### Bind exact source and skill revisions; never repair by substitution

**Decision**: Every inquiry binds a `RecordRefV1` with stable ID, revision,
and content digest for the source and supplied skill version. The second
inquiry records that exact ref, the references delivered, and separately the
recipe execution refs. Semantic incompatibility, lost grant, stale freshness,
failed join validation, or revoked authority prevents current guidance from
entering context. It leaves prior locks and historical usage untouched. A
new version may be used only by a separately admitted inquiry whose input
binding explicitly names it.

**Rationale**: The shared interface forbids mutable `latest` input refs and
the canonical state contract preserves immutable versions and old pins.
Feature 015 FR-001 requires the same complete compatibility predicate for
identity, grants, lifecycle, meaning, freshness, and recipe privileges.

**Alternatives considered**: Match table names or schema fingerprints;
rejected because equal schemas may have different meaning. Automatically
refresh an incompatible input; rejected because it silently changes a
locked analysis.

### Exercise drift at the level of affected guidance

**Decision**: Seed same-schema metric meaning, unit/key, tenant/environment,
role/grant, timezone/category, watermark, and join-window mutations. Assert
that the shared 015 predicate rejects/stales only affected guidance. Also
seed an unused column and assert unrelated active assertions and recipes
remain compatible.

**Rationale**: Feature 004 SC-002/SC-009 and feature 015 SC-002/SC-006
explicitly require scoped invalidation and protection from broad
schema-fingerprint invalidation.

**Alternatives considered**: Invalidate the entire package on every schema
hash change; rejected because an unused column cannot stale unrelated
validated facts.

### Reuse only synthetic, local, bounded fixtures

**Decision**: Use the SQLite fixture and limits already specified in feature
014: at most 100 visible tables, 200 columns per table, 20 profiled tables,
1,000 sampled rows per table, and 30 seconds per discovery profile. Include
contaminated comments, sensitive metadata/row sentinels, study-specific
outcome content, an over-budget source, and a recipe whose grant exceeds the
intended audience. No reference answer enters worker context.

**Rationale**: The 004 assumptions and 014 design select one bounded,
read-only relational fixture. Proposal §13 requires source text and metadata
to remain inert/scoped and forbids credentials, raw rows, and untagged
outcomes in general guidance.

**Alternatives considered**: Add a real connector or production source;
rejected because it is outside the accepted scope and unnecessary to prove
the frozen source-memory contract.

### Show provenance in the existing evidence inspector

**Decision**: Assert source/skill provenance through the frozen 011 paths used
by the ordinary commands: `answer show --inquiry <id> --open` resolves to
`GET /api/analytics/inquiries/{inquiryId}/answer`, and `trace --claim <id>`
resolves to `GET /api/analytics/claims/{claimId}/trace`. The 004 fixture
starts the existing handler with committed synthetic records and verifies
that the answer/trace responses' generic linked-provenance projection exposes
links resolving to exact source,
skill, independent-validation, configured-owner decision, supplied-reference,
and executed-recipe `RecordRefV1` values (ID, revision, digest) under the
current viewer grant. It does not add a 004 route or feature-specific DTO.
The projection must distinguish source ID/version, environment/tenant,
current role/audience, definitions scope, skill lifecycle/status,
compatibility reason, and exposure. It must not claim the model understood
every supplied reference.

**Rationale**: Proposal §§13 and 15 and interfaces.md require an exact
source/skill trace and distinguish delivered references from actually
executed recipes. Feature 011 owns the answer/trace HTTP and viewer paths;
004 is assigned the synthetic producer/consumer test and exercises those
existing routes with committed 014/015/022 refs.

**Alternatives considered**: Add a new 004 inspector or API; rejected because
011 owns the viewer and its route contract. Treat a supplied entrypoint as
executed use; rejected by feature 015 and T19.

## Planning validation boundary

The task-plan stage checks only feature-local artifact structure, coverage,
unit ownership, command safety, dependencies, and absence of product-code
edits with `node scripts/build/check-plan.mjs 004`. It does not run product
tests or claim implementation acceptance. The accepted spec, checklist,
fixture/reference answers, and policy thresholds remain unchanged.
