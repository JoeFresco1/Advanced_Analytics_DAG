# Source Memory Integration Contract

This contract defines feature 004's use of frozen providers and user paths.
It adds no new CLI command, shared controller API, registry entry, database
adapter, viewer route, or persisted schema.

## Owned path and providers

The live user path is the frozen `dag analytics ask --project <path>
--question <text> --source <fixture> --model gpt-6-luna --reasoning xhigh`.
On a cold source, feature 014's registered `orient` hook calls the same
grant- and budget-bounded discovery implementation used by the registered
`source discover` command. That implementation accepts an opaque registered
source ID; it does not accept a worker-supplied connection path, SQL, grant,
or executable. Feature 014 checks metadata/aggregate grants, uses its bounded
read-only SQLite stage, commits a scoped candidate through feature 009, and
returns only the candidate's safe reference to orientation. Candidate content
is not supplied as active guidance.

Feature 015's `source-reuse` provider consumes the current source-skill
registry through `AnalyticsExtensionV1`. It applies the one compatibility
predicate from `src/analytics/skill-compatibility.ts`; 004 must not implement
or call a duplicate predicate. The normal extension lifecycle supplies
orientation references and creates exact usage events. Feature 022 is a graph
prerequisite and owns the only production activation/revocation transition
for these source candidates. Feature 003 owns the CLI/controller/loader and
feature 011 owns the answer and trace viewer/API.

## First inquiry, validation, and second inquiry

The first ordinary synthetic `ask` binds its question to a stable source ID
and version. If no compatible active guide exists, 014's `orient` hook calls
the same bounded production discovery implementation as `source discover`
under the current principal/grant. It records the candidate package,
assertion provenance, discovery bounds, recipe tests, and stop reason, then
returns only a safe candidate reference to orientation. An independent
bounded recipe replay under the intended grant and a
configured source-owner decision are prerequisites for an immutable active
version; a process exit, worker recommendation, or candidate status alone is
not enough. A denied decision or unresolved authoritative meaning leaves the
guide `candidate` and unavailable as active guidance.

After the ordinary ask has caused 014 to create the candidate, the demo
independently replays a bounded recipe under the intended grant and retains
that execution ref separately. It then invokes 022's registered `skills
review` command through 003's normal typed command registry, using the exact
candidate ref, configured owner, approval decision, substantive rationale,
and expected project revision. The 022 provider alone commits an active
version after required independent validation and conflict checks pass. A
missing/failed replay, unknown definition, unresolved conflict, or owner
rejection leaves the package `candidate`; retain the failure without retrying
until favorable. Tests assert zero self-activation and the live demo does not
seed active records. A second, separately initiated ordinary `ask` runs only
after the 022 decision commits. Feature 015's registered `orient` hook
performs a fresh compatibility assessment and pins the exact active skill version
and exact source version that the controller actually supplies. The record
distinguishes:

1. references supplied to the worker;
2. recipes it actually executes under the current grant;
3. prior-result exposure, if any; and
4. the worker's attributed use/rejection reason, if present.

The existing 011 generic linked-provenance projection shows the immutable
ID/revision/digest and validation/authority refs for each source and skill
binding under current grants. Presence in context does not prove
comprehension.

## Compatibility, invalidation, and locked inputs

The compatibility result follows feature 015's complete predicate. In
particular, same schema does not imply same meaning. Environment/tenant,
current role/grant/audience, key/grain, units/timezone, category/metric
definitions, source version, watermark/freshness, recipe access, and lifecycle
state all remain material. A changed assumption marks only affected facts
`stale`; source-owner withdrawal is `revoked`. An unused column leaves
unrelated facts active.

The inquiry's admitted source and skill refs are immutable. A compatibility
failure, denied grant, stale watermark, failed later-window join, or revoked
authority cannot silently replace either ref. A proposed refreshed guide may
be used only by a new or explicitly revised inquiry that pins the exact
compatible source and guide versions and preserves prior exposure/history.
If no such version is authorized, the answer exposes unresolved meaning or a
canonical non-answer rather than asserting current support.

Feature 004 invokes feature 022's registered `skills revoke` command as the
configured owner, passing the exact active version and withdrawal rationale
under the current expected project revision. The resulting immutable 022
record is consumed by a later 015 orientation. The new inquiry receives zero
current guidance from the revoked version while earlier inquiries keep their
original exact pin. No revocation state or event is seeded around the
provider.

## Access, data handling, and guarantees

- The current principal, grant, operation, audience, and effective period
  are checked by the owning provider before reads and by the viewer at
  inspection time.
- Source text remains untrusted; it cannot change system instructions,
  policy, role, access, query bounds, or output paths.
- Packages contain no credentials, raw sensitive rows, broader-audience
  metadata, or untagged study-specific outcomes.
- Discovery and recipe limits are those already frozen in feature 014.
- All examples use synthetic/local data. If the local harness can read fixture
  files through a broader shell, the UI labels the boundary as workflow-level
  and does not claim enforced filesystem isolation.
- A valid null, negative, inconclusive, or conflicting answer remains an
  analytical outcome. Check failure and process status remain separate.

## Inspector evidence

The answer/trace inspection path is the existing feature 011 surface, reached
through `dag analytics answer show --inquiry <id> --open` and
`dag analytics trace --claim <id>`. Its frozen shell routes are
`/analytics/inquiries/{inquiryId}` and `/analytics/claims/{claimId}`; its JSON
routes are `GET /api/analytics/inquiries/{inquiryId}/answer` and
`GET /api/analytics/claims/{claimId}/trace`. The 004 fixture starts the
existing handler with committed synthetic records and follows its returned
evidence links to the source and skill `RecordRefV1`s and to their 014
assertion, 022 validation/authority, and 015 usage records. It asserts the
exact IDs, revisions, and digests under the current viewer grant. 011's
existing generic linked-provenance projection displays source
identity/version and skill version/state;
environment, tenant, role, audience and definitions scope; assertion
provenance; independent recipe validation; references supplied; recipes
executed; exposure; compatibility/invalidation reason; and owner authority.
Missing, stale, revoked, redacted, denied, or unavailable links remain
visible with an access-safe reason. No generic "verified" badge substitutes
for these dimensions. Feature 004 consumes the frozen routes and
generic response projection; it
adds no viewer, route, or feature-specific DTO.

## Required acceptance evidence

The feature fixture demonstrates a first ordinary ask whose 014 `orient` hook
performs bounded discovery and commits a candidate;
an independent bounded recipe replay and 022 authority review; a second
ordinary ask's exact version use with an executed recipe and fewer discovery steps;
complete
same-schema semantic/key/role/freshness invalidation; unaffected guidance
after an unused-column change; scoped failed-join invalidation; access,
privacy, source-text, and outcome-exposure boundaries; and an authoritative
revocation that blocks new use while preserving an old inquiry's exact pin.
The evidence is mapped to all feature requirements, success criteria, and
ordered edge cases in `execution.json` and `verification.md`.
