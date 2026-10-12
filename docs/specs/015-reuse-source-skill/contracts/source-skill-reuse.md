# Source-Skill Reuse Contract

This feature-local contract consumes the frozen 003 extension shape, 009
project publication primitive, 014 source-skill candidate model, and 022
activation/revocation authority. It does not change those shared interfaces.

## Compatibility predicate

`src/analytics/skill-compatibility.ts` exports the one normative predicate:

```ts
compareSourceSkillCompatibilityV1(
  input: SourceSkillCompatibilityInputV1
): SourceSkillCompatibilityAssessmentV1
```

The input binds an exact immutable skill version, current source identity and
version, principal and current grant, current lifecycle, current source facts,
per-guidance fact dependencies and recipe privilege requirements, and any
cadence/drift-triggered profile evidence. The assessment lists each required
dimension as equal, changed, or unresolved and names impacted guidance. It
returns an operation decision of compatible, refresh-required, unresolved,
revoked, or access-denied. These operation decisions do not replace canonical
skill lifecycle states or current-validity values.

Required dimensions are source identity; environment and tenant; source role,
audience and current grant; lifecycle; required schema fields and key/grain
meaning; units and timezone; category and metric definitions; source-version
compatibility evidence; watermark/freshness/lag; and recipe privileges. Any
required unknown or mismatch prevents silent current guidance. A source
version change is usable only with its declared compatibility evidence and
fresh required profile result. A whole-schema difference is not a mismatch
when the changed field is unused by every selected item.

Cheap checks run on every orientation. Expensive profile requests are
triggered by declared cadence or relevant drift; until the exact check result
is available and current, impacted guidance remains unavailable for current
use. The profile scheduler cannot independently approve compatibility.

## Provider and durable events

`src/analytics/skill-refresh.ts` exports
`analyticsExtensionV1: AnalyticsExtensionV1` using `extensionId: "source-reuse"`
and `dependsOn: ["source-discovery"]`. Its orientation hook:

1. Applies the 003 authenticated principal and 001 current source grant before
   loading any entrypoint, reference title, or recipe.
2. Resolves the exact 014 skill version and calls the single compatibility
   predicate. It supplies active instructions only for lifecycle `active`
   when the predicate allows current use. A `candidate` is never supplied as
   instructions regardless of compatibility, role/audience, or freshness; an
   authorized lookup may return only an access-safe candidate reference for
   review, while a denied scope returns no reference. `stale` and `revoked`
   versions never supply current instructions; a stale historical pointer
   includes its limitation, and a revoked version supplies no current guide.
3. Records skill version and reference supply in a project memory event. It
   records execution only from the exact recipe's execution callback.
4. Calls the existing exposure service for any separately stored study or
   holdout finding that enters context.

Refresh/staleness records use 009 `commitProjectMemory` with expected
manifest revision and stable commit ID. New skill content has a new immutable
version and predecessor link. 015 honors 022 lifecycle authority, never
activates an unapproved candidate or performs revocation, and never migrates
older run pins or locked plans.

The acceptance invokes this provider through the ordinary 003 inquiry
orientation caller and registered extension loader, then observes durable
project-memory effects through the real 009 writer. Provider-only and
metadata-only assertions do not establish this integration.

## Required observable evidence

- Exact skill `RecordRef` and entrypoint/reference refs supplied to each
  inquiry.
- Separate recipe-supplied and recipe-executed refs.
- The compatibility assessment's exact source/grant/version bindings,
  dimension results, freshness/profile evidence and affected guidance IDs.
- Exposure event for a supplied study/holdout outcome.
- Immutable predecessor/successor, staleness, validation, and authority refs
  for refresh and revocation.
- Append-only project commit/ref sufficient to recover after a lost
  acknowledgement; no custom registry or mutable history file.
