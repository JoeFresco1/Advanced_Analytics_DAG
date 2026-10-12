# Research: Reuse and Refresh a Source Skill

This bounded planning pass resolves implementation choices from the accepted
product and shared interface contracts. No external research or unresolved
technology decision is needed.

## Decision: One normative predicate owns compatibility

**Decision**: Implement `compareSourceSkillCompatibilityV1` only in
`src/analytics/skill-compatibility.ts`. It assesses all dimensions in FR-001
and returns the dimension-level assessment and affected guidance references.
`skill-refresh.ts` and future 014/004/022 callers consume this function rather
than recreating compatibility logic.

**Rationale**: The acceptance contract requires changed units, roles,
timezone/lag, keys/grain, definitions, source version, and grant to be caught
by the same predicate. A schema-only hash misses semantic changes; a global
schema hash would invalidate guidance for irrelevant additions. The proposal
§13 requires relevant-scope compatibility before reuse.

**Alternatives considered**: Schema fingerprint equality was rejected because
the proposal and SC-002/SC-006 require same-schema semantic drift detection
and tolerance of unused added columns. Separate identity and freshness
predicates were rejected as normative compatibility decisions because they
could disagree about whether a skill may guide current work.

## Decision: Cheap metadata checks per orientation; profiles on cadence or drift

**Decision**: Check source identity, environment/tenant, current grant and
audience, lifecycle, relevant fact bindings, source version evidence, recipe
privileges, and cheap watermark/lag bounds on every orientation. Store the
profile rule and last exact source/check binding with the skill. Run expensive
join/profile checks only when the declared cadence is due or a changed fact
triggers them. A due-but-unrun profile leaves affected guidance unavailable
for current use.

**Rationale**: Proposal §13 explicitly separates cheap identity,
compatibility, and freshness checks from expensive profile checks and allows
the latter at a declared cadence or drift trigger. Fail-closed unknowns and
freshness assumptions are required by FR-001 and the canonical `unknown`
sentinel.

**Alternatives considered**: Full profiling on every query was rejected as
unbounded and contrary to the cadence contract. Treating an unexpired prior
profile as proof for an unobserved source version was rejected because
freshness and source version are required dimensions.

## Decision: Bind semantics to guidance dependencies

**Decision**: Each guidance reference, assertion, and recipe lists stable
fact references for its key/grain, unit/timezone, category/metric
definitions, freshness, and privilege assumptions. A difference or unknown
among these facts affects only dependents. An added but unused column does
not stale the whole source skill.

**Rationale**: FR-003, SC-002, SC-006 and proposal §13 require scoped
staleness. This design makes invalidation deterministic and reviewable while
retaining the source owner/observation provenance from feature 014.

**Alternatives considered**: Whole-skill invalidation for any schema change
was rejected because it over-invalidates on unused columns. Text similarity
or worker interpretation alone was rejected for comparing authoritative
metadata and exact bindings.

## Decision: Append-only refresh through 009; lifecycle authority stays in 022

**Decision**: Refresh creates a new immutable skill-version record with a
predecessor reference and commits it, alongside append-only changed-fact and
staleness events, through `commitProjectMemory`. 015 may record a refreshed
candidate or choose an already validated active replacement. It does not
activate candidates or perform the `revoked` transition; it honors 022's
current authority state and denies new guidance from a revoked version.

**Rationale**: The shared interfaces make 009 the project commit/CAS owner,
and the canonical skill lifecycle assigns activation to 022 and reuse/stale
handling to 015. Historical pins must remain exact after refresh or
revocation.

**Alternatives considered**: In-place update was rejected because it would
rewrite prior pins. A second writer or feature-local registry was rejected
because it would bypass the project manifest and CAS. Having 015 revoke a
skill itself was rejected because that authority transition belongs to 022.

## Decision: Reuse the frozen 003 provider seam

**Decision**: Export `analyticsExtensionV1` from
`src/analytics/skill-refresh.ts`, with `extensionId: "source-reuse"` and
`dependsOn: ["source-discovery"]`. It supplies the compatible exact entrypoint
and authorized references and emits events through context services. It does
not edit `extensions.ts` or own command dispatch.

**Rationale**: `docs/build/interfaces.md` assigns this provider and requires
domain providers to use the fixed allowlist, lifecycle hooks, context, and
dependency ordering. 003 already owns the registry; 009 owns memory writes.

**Alternatives considered**: Direct controller/CLI edits or filesystem plugin
loading were rejected because they violate frozen graph ownership. Persisting
events in a custom file/index was rejected because it would bypass 009 and
compete with 019's projection.

## Decision: Supplied, exposed, and executed are separate records

**Decision**: Pin the exact skill version and each supplied reference.
Persist actual recipe execution only after the recipe runner acknowledges
that exact recipe. Keep the supplied-but-unexecuted case as a supply event
with no execution event. If a distinct study/holdout outcome summary is
loaded, preserve its provenance and append exposure through the controller
service.

**Rationale**: FR-002/FR-005 and SC-003/SC-007 require observable supply,
execution, and exposure distinctions. Proposal §13 and I17 say delivery does
not prove comprehension and loading outcome material cannot erase exposure.

**Alternatives considered**: Treating all supplied recipes as used was
rejected because it falsely reports consumption. Folding study outcomes into
general instructions was rejected because result memory and skill memory are
separate authorities.
