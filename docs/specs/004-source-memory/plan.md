# Implementation Plan: Learn and Reuse a Data Source

**Branch**: `004-source-memory`  
**Date**: 2026-09-28  
**Spec**: [spec.md](spec.md)

**Input**: Accepted feature specification in `specs/004-source-memory/spec.md`.

## Summary

Integrate the source-discovery provider from feature 014 and the compatibility
and reuse provider from feature 015 into the ordinary `dag analytics ask`
orientation flow owned by feature 003. Feature 022 is a graph prerequisite.
On the first ordinary question, the registered feature 014 `orient` hook
calls the same grant- and budget-bounded discovery implementation used by
`source discover` against one synthetic SQLite source. It commits an
immutable candidate with assertion and recipe provenance, then returns only
the candidate's safe reference to orientation; candidate content is not
supplied as active guidance. After a separate bounded recipe replay, the demo
invokes feature 022's registered `skills review` command through 003's normal
typed command registry with the exact candidate and configured owner. Only a
successful independent validation, resolved definition/conflict checks, and
recorded owner approval can create the immutable active version. Failed or
missing validation, unknown meaning, or a conflict leaves the package a
candidate; failures are retained and are not retried until a favorable result
appears. No fixture seeds an active record or bypasses the provider. A
second independent live question then reaches feature 015's registered
`orient` hook and receives that exact active version, selected references,
and an executable recipe.

The integration verifies 011's generic linked-provenance answer/trace
projection distinguishes source identity, skill identity and lifecycle,
supplied references, executed recipes, validation provenance, access
audience, and exposure under current grants. Same-schema semantic
changes, role/freshness drift, failed join validation, a revoked authority,
and source-content privacy fixtures must leave the locked source or skill
reference untouched and must never silently substitute a different input.

The work is limited to the graph-owned integration outputs:
`src/analytics/demo-source-memory.ts` and
`src/analytics/acceptance/004.test.ts`. It consumes the 003 extension
registry, 014 discovery, 015's single compatibility predicate, 009 project
memory, and the existing answer/trace viewer. It does not implement a second
registry, source adapter, compatibility predicate, viewer, or activation
service.

## Technical Context

**Language/Version**: TypeScript 5.6 on the repository's Node.js `>=20`
runtime; source recipes use the repository's uv-managed Python interpreter.

**Primary Dependencies**: Existing 003 analytics ask/controller and extension
interfaces; 014 source-discovery provider; 015 source-reuse provider and
`skill-compatibility.ts`; 022 `skill-activation` provider; 009
`commitProjectMemory`; 003's typed command registry; 011's generic linked-
provenance answer/trace APIs and viewer; local SQLite and Node's test runner.
No new dependency or package manifest change.

**Storage**: Synthetic SQLite fixture inputs are local and read-only. Source
packages and immutable skill/lifecycle records use the established
project-memory manifest and CAS in `.dag-analytics/`; inquiry-specific exact
source and skill references remain bound in the run sidecar. The SQLite
catalog is only a rebuildable projection.

**Testing**: The feature acceptance suite is
`src/analytics/acceptance/004.test.ts`, run with the repository's `pnpm test`.
The live demonstration uses `gpt-6-luna` with `xhigh` reasoning and only
synthetic/local fixtures. Planning does not execute product tests.

**Target Platform**: Local analytics CLI/controller and the frozen extension
registry, with the existing neutral execution stage and browser answer/trace
viewer.

**Project Type**: TypeScript analytics CLI/controller integration with
local Python/SQLite recipe execution and a browser evidence inspector.

**Performance Goals**: Reuse feature 014's frozen discovery bounds: no more
than 100 visible tables, 200 columns per table, 20 profiled tables, 1,000
sampled rows per table, and 30 seconds for one discovery profile. Stop on the
first reached cap and retain its reason. The second inquiry performs fewer
discovery steps than the cold inquiry while retaining required checks.

**Constraints**: No `dag.run.json` changes, shared-file edits, new provider
registration, graph changes, or alternate runner. Resolve grants before
source reads and current viewer access before evidence display. Preserve
locked source bindings, exact `RecordRefV1` revision/digest references,
append-only project history, canonical lifecycle values, and attribution of
worker judgments versus owner decisions. Do not copy credentials, raw
sensitive records, unrestricted source comments, or untagged study outcomes
into skill context. A broad local worker shell remains a workflow boundary,
not a claim of filesystem isolation.

## Governing Design Inputs

- [Shared architecture](../../docs/build/ARCHITECTURE.md) and
  [frozen interfaces](../../docs/build/interfaces.md): TypeScript facades,
  `AnalyticsExtensionV1`, controller lifecycle hooks, `RecordRefV1`, the
  source-discovery/source-reuse provider ownership, and the normal `ask`,
  `source discover`, `answer show`, and `trace` paths.
- [Feature graph](../../docs/build/graph.json): dependencies 003, 014, 015,
  and 022; this feature's three planning files, live demonstration, and
  acceptance fixture are the complete assigned outputs.
- [Feature 014 source-discovery contract](../014-build-source-skill/contracts/source-discovery.md):
  discovery remains candidate-only, source text is untrusted, and access and
  recipe bounds are enforced by its provider.
- [Feature 015 specification](../015-reuse-source-skill/spec.md): the one
  normative compatibility predicate, exact supplied version, scoped
  invalidation, and historical pin behavior.
- [Feature 022 provider contract](../022-activate-reusable-skills/contracts/skill-activation-v1.md):
  registered `skills review` and `skills revoke` commands, independent
  validation, configured-owner authority, append-only lifecycle events, and
  scoped orientation. This is the only activation/revocation path used by
  the live demo.
- [Feature 011 HTTP contract](../011-inspect-result-trace/contracts/http.md):
  `answer show --open` uses `/analytics/inquiries/{inquiryId}` and the answer
  API `/api/analytics/inquiries/{inquiryId}/answer`; `trace --claim` uses
  `/analytics/claims/{claimId}` and
  `/api/analytics/claims/{claimId}/trace`. The 004 acceptance fixture consumes
  these existing viewer paths and their committed evidence refs; it adds no
  viewer route, API, or presentation schema.
- [Canonical states](../../docs/specs/state-vocabularies.md): skill lifecycle
  is `candidate`, `active`, `stale`, or `revoked`; unknown dimensions remain
  unknown; inquiry outcomes follow evidence and canonical precedence.

## Constitution Check

### Pre-design gate

- **Honest answer and evidence**: The user submits ordinary questions; a
  process exit does not imply analytical support. Source and recipe evidence
  remain bound to exact versions and outcomes.
- **Append-only history**: Each inquiry retains its locked source and skill
  refs. Refresh, invalidation, or authority withdrawal creates a linked
  version/event; it does not rewrite prior inquiries.
- **Scoped and tested memory**: Source guidance is audience-bound, typed by
  assertion provenance, independently recipe-tested, and separate from
  study-specific results.
- **Access and judgment**: The worker cannot invent grants or authority. The
  source owner/controller decision and compatibility judgments retain their
  actor, reason, and evidence refs.
- **Bounded local behavior**: Discovery and recipes use the 014 bounds; local
  shell guarantees are labeled accurately.
- **End-to-end delivery**: A first ordinary ask triggers 014's production
  orientation hook, which commits a candidate using the bounded discovery
  implementation and returns only its safe ref. After 022 validates and
  activates it, a second ordinary ask consumes the exact active version
  through 015; 011's current-grant projection exposes the linked provenance.

**Gate result**: Pass under the existing contracts; no constitution
exception, policy change, or accepted-criteria change is proposed.

### Post-design gate

The design uses 003's ordinary `ask` lifecycle and typed command registry,
014's production `orient` hook and shared bounded discovery implementation,
015's production `orient` hook and normative compatibility predicate, 022's
registered review/revocation commands, 009 project publication, and 011's
existing answer/trace paths. It confines implementation to the two 004 graph
outputs. On a cold first ask, 014 commits a candidate and returns only its
safe ref. After independent recipe replay, the live demonstration submits
that exact candidate to 022 `skills review` as the configured owner; the
provider records the decision and alone performs any activation. Unknown or
conflicting definitions and failed validation remain candidates. The
revocation case calls 022 `skills revoke` as the configured owner. No active
or revoked state is seeded around those transitions. The second inquiry uses
015's production orientation hook only after successful provider-backed
activation. Semantic incompatibility and revocation block current use while
preserving historical pins and locked inputs. Acceptance executes both
ordinary asks and inspects committed links through 011's existing shell and
answer/trace APIs. The API's generic linked-provenance projection exposes the
source, skill/status, validation, authority, supplied-reference,
executed-recipe, and exposure refs under current grants; 004 adds no
dispatcher, viewer route, or feature-specific DTO.

**Gate result**: Pass. The integration introduces no duplicate source state,
compatibility rule, scheduler, project writer, or UI ownership.

## Product Flow and Failure Boundaries

1. Start a normal live `dag analytics ask` with a synthetic source ID and the
   first question. Resolve principal and metadata/aggregate grants before
   discovery.
2. During the first ordinary `ask`, feature 014's registered `orient` hook
   checks for a compatible active guide. On this cold source it calls the
   same read-only, grant- and budget-bounded discovery implementation as the
   registered `source discover` command. It records the exact source
   version/window, grant, bounds, assertion provenance, and recipe execution;
   it commits an immutable `candidate` through 009 and returns only its safe
   reference to orientation. Candidate content is not supplied as active
   guidance, and the hook does not activate it.
3. Replay at least one bounded recipe independently under the intended grant.
   Invoke the registered 022 `skills review` command through 003's normal
   typed command registry with the exact candidate, configured owner,
   decision, rationale, and expected project revision. The provider
   records the owner decision and any required conflict check, then commits an
   immutable `active` version only after validation and authority pass. A
   denied/missing authority, failed replay, unknown definition, or unresolved
   conflict leaves the package `candidate`; retain a failed result without
   retrying until it passes. No fixture pre-seeds an active version.
4. Only after 022 commits that active version, start a second independent
   ordinary live `ask`. Feature 015's registered `orient` hook applies its
   normative compatibility predicate once, pins the exact source and skill
   `RecordRef` revisions/digests actually supplied, records references
   supplied apart from recipes executed, and preserves the existing required
   checks.
5. Open the answer using `dag analytics answer show --inquiry <id> --open`
   and its existing 011 shell route `/analytics/inquiries/{inquiryId}`; read
   the committed answer via `GET /api/analytics/inquiries/{inquiryId}/answer`.
   Open its claim through `dag analytics trace --claim <id>` and
   `/analytics/claims/{claimId}`, then read
   `GET /api/analytics/claims/{claimId}/trace`. The 004 fixture asserts that
   the 011 generic linked-provenance projection resolves committed exact
   source, skill/status, validation, authority, supplied-reference,
   executed-recipe, and exposure refs under the current viewer grant. It
   consumes these 011 paths and does not define another route or
   feature-specific DTO. Inspect source
   identity/environment/tenant/role/definitions and source-version binding;
   skill version/status/authority/validation provenance; supplied-reference
   and executed-recipe events; and exposure or an explicit absence of
   outcome-bearing guidance.
6. Repeat orientation with synthetic mutations. A same-schema meaning,
   tenant, grant, unit/key, timezone, category, watermark, or tested-join
   mismatch marks only affected guidance stale or incompatible and excludes
   it from current instructions. A revoked authority excludes the version
   from new context. Revoke the active version with the configured owner's
   022 `skills revoke` command so the provider writes the authoritative
   transition. In both cases the inquiry's already locked source/skill
   input remains unchanged; the controller may report unresolved meaning or
   ask for input, but it does not silently substitute a refreshed source or
   guide. The owner revocation is written by 022 `skills revoke`, and the
   inspector is read through 011's existing answer/trace APIs.

## Project Structure

### Planning artifacts

```text
specs/004-source-memory/
├── plan.md
├── research.md
├── data-model.md
├── contracts/
│   └── source-memory.md
├── quickstart.md
├── tasks.md
├── execution.json
└── verification.md
```

### Graph-owned integration code

```text
src/analytics/
├── demo-source-memory.ts
└── acceptance/
    └── 004.test.ts
```

`demo-source-memory.ts` drives the standard controller/extension path; it is
not a second CLI or source facade. Acceptance fixtures are synthetic and
local. Package bytes and durable record schemas remain owned by features
014/015/009; answer rendering remains owned by 011.

## Complexity Tracking

No constitution violations or new architecture layers are proposed. The
fixture invokes 022's existing review/revocation commands for the configured
owner decision and lifecycle events; 004 adds no activation, revocation, or
viewer API.
