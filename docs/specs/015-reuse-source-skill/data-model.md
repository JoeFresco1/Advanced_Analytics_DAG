# Data Model: Reuse and Refresh a Source Skill

All durable records use the shared stable ID, revision, content digest, actor,
provenance and access-scope conventions. Project persistence and recovery use
the 009 `commitProjectMemory` API. The SQLite index is a 019-owned projection,
not an authority.

## SourceSkillVersionV1

An immutable scoped source-guidance version created by 014 or refresh.

| Field | Meaning / validation |
| --- | --- |
| `skillRef` | Stable record ID, revision, digest. A supplied inquiry pins this exact reference. |
| `predecessorRef` | Prior immutable version when refreshed; absent for initial source discovery. |
| `sourceScope` | Source identity, environment, tenant, role, audience, and declared access scope. Values come from 014/owner metadata, never worker privilege claims. |
| `sourceBindings` | Source version and relevant schema/key/grain, units/timezone, category/metric definitions, watermark and freshness facts, each with provenance and version/digest. |
| `guidanceItems` | Entry point, reference, assertion, or recipe records. Each item names its affected fact references. |
| `recipeRequirements` | Bounded operations and parameters needed by each recipe; checked against the current grant on every use. |
| `profilePolicy` | Declared expensive-check cadence, drift triggers, bounds and validation provenance. A pending check cannot authorize current use. |
| `lifecycle` | Canonical state `candidate`, `active`, `stale`, or `revoked`, plus authority/event reference. 022 owns activation and revocation. |
| `validationRefs` | Exact independent validation/check refs, tested source versions/windows, outcome, limits, and timestamps. |
| `studyOutcomeRefs` | Separate, provenance-tagged outcome references; loading one creates exposure rather than generalizing it into the source guide. |

## GuidanceFactBindingV1

Stable fact identity and meaning used by one guidance item. It records the
fact kind (source identity, tenant/environment, role/audience, schema field,
key/grain, unit, timezone, category/metric definition, source version,
watermark/freshness, or recipe privilege), authoritative/observed/tested/
inferred provenance, value digest or explicit `unknown`, observation window,
and validation time. The fact ID is scoped; it does not identify a whole
schema snapshot. Consequently an unrelated added column has no dependency
edge to existing guidance.

## SkillCompatibilityAssessmentV1

An attributed, version-bound result from the single normative predicate.

| Field | Meaning / validation |
| --- | --- |
| `skillRef`, `currentSourceRef`, `principalRef`, `grantRef` | Exact inputs assessed. Missing principal or grant denies. |
| `checkedAt`, `predicateVersion` | Compatibility evaluation identity and implementation version. |
| `dimensionResults` | For each required dimension: equal, changed, or unresolved with source evidence refs and a safe reason. |
| `affectedGuidanceRefs` | Exact guidance blocked/staled by changed or unresolved bindings. |
| `profileCheck` | Not due, due, pending, or exact profile result reference. This schedules validation; it does not create a second compatibility predicate. |
| `currentUse` | Operation result: compatible, refresh-required, unresolved, revoked, or access-denied. This is not a new canonical skill lifecycle state. |
| `historicalAccess` | Whether an access-safe historical reference may be shown and its limitation; never includes a current recipe when it is stale/revoked/unauthorized. |

## SkillSupplyEventV1 and RecipeUseEventV1

Append-only project events record (1) exact skill version, entrypoint and
references supplied to a specific inquiry, with current compatibility and
grant assessments, and (2) only the recipes that the execution adapter
actually ran, bound to the supplied skill/reference and execution. These are
separate events; absence of a recipe-use event means the recipe was not
recorded as executed. Any outcome reference supplied also has an exposure
event and its source-study provenance.

## SkillStalenessEventV1 and Refresh

A changed/unknown relevant fact records the old version, fact reference,
affected guidance IDs, cause, and dependent pinned inquiries for review. A
refresh creates a new `SourceSkillVersionV1` linked to its predecessor and a
refresh event. The old version's bytes, original inquiry pins and execution
history remain. Current status is a derived view over immutable versions and
events. A later `revoked` event from 022 blocks future guidance without
deleting an existing run pin.

## Relationships and constraints

```text
source skill version ──> scoped fact bindings ──> guidance items / recipes
        │                         │                         │
        ├──> compatibility assessment ──> supply event     └──> executed-use event
        ├──> predecessor/successor refresh link
        └──> staleness/revocation events ──> dependent inquiry pins
study outcome reference ──> exposure event ──> inquiry exposure history
```

- Current grant and role are checked before references enter worker context.
- Any required unknown or mismatch blocks silent current guidance.
- Changed facts stale only guidance items that reference those facts; whole
  skill staleness is reserved for facts on which all usable guidance depends.
- A refreshed version is not automatically active; 022 owns authority.
- A revoked pin remains historically resolvable but cannot supply new current
  guidance.
- Commit retries reuse stable IDs; changed content under an existing commit
  ID is rejected by 009.
