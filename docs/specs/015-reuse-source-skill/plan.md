# Implementation Plan: Reuse and Refresh a Source Skill

**Branch**: `015-reuse-source-skill` | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from `specs/015-reuse-source-skill/spec.md`

## Summary

Implement one complete, deterministic source-skill compatibility predicate and use it for later-worker orientation, semantic drift, freshness, access scope, lifecycle, and recipe privilege checks. The 015 provider supplies the exact compatible entrypoint and references, records exposure and exact version pins, distinguishes supplied recipes from recipes actually executed, and commits immutable refresh and staleness events through 009 project memory. Cheap identity, grant, relevant-meaning, version, and freshness checks run on every orientation; bounded profile checks run at the declared cadence or after a drift trigger. Invalidation follows each guidance item's declared fact dependencies so an unused added column does not stale unrelated guidance.

The implementation stays within the graph-owned files: `src/analytics/skill-compatibility.ts`, `src/analytics/skill-refresh.ts`, and `src/analytics/acceptance/015.test.ts`. It exports `analyticsExtensionV1` from `skill-refresh.ts` for the frozen 003 registry. Feature 022 remains the authority for activation and revocation transitions; 015 honors the current lifecycle and cannot create or undo a `revoked` transition.

## Technical Context

**Language/Version**: TypeScript, Node.js 20+, ECMAScript modules.

**Primary Dependencies**: Existing TypeScript types and services from the 003 extension registry; source-skill candidates and provenance from 014; project-scope `commitProjectMemory` and recovery/CAS from 009; built-in `node:test` and existing `tsx` development dependency for the focused TypeScript acceptance file. No new package dependency is required.

**Storage**: Project-scope append-only skill, use, exposure, staleness, and refresh records are committed through 009 `commitProjectMemory`. The SQLite/catalog projection remains 019-owned. Skill versions are immutable records; a new refresh links to its predecessor and never rewrites an inquiry's pin.

**Testing**: Synthetic/local fixtures in `src/analytics/acceptance/015.test.ts`. The focused run is `pnpm exec tsx --test src/analytics/acceptance/015.test.ts`; the final graph unit also runs `pnpm build` and `pnpm test`. The acceptance invokes the ordinary 003 inquiry-orientation caller through its registered extension loader and dependency ordering, and observes project writes through the 009 writer; direct-provider-only and metadata-only checks are insufficient. Checks cover exact supplied version and entrypoint, actual recipe execution, retention of every required check on warm reuse, candidate exclusion and all four canonical lifecycle states, the complete predicate's changed and unknown dimensions (including source identity/environment and source-version evidence), scoped invalidation, profile cadence and drift triggers, immutable refresh with a validated replacement, access narrowing, revoked versions, prior-outcome exposure, and evaluation-boundary preservation.

**Target Platform**: Local analytics CLI/controller with the configured Node.js runtime and the existing TypeScript project build.

**Project Type**: Analytics product feature and controller extension under `src/analytics/`; no runner, CLI registry, package manifest, shared architecture, or feature-spec edits.

**Performance Goals**: Keep every-orientation checks bounded to metadata and current-grant reads. Run expensive joins/profiles only when a stored cadence is due or a declared source drift trigger fires. Do not scan unrelated columns or profile a full source as a side effect of reuse.

**Constraints**: Required unknowns or mismatches cannot silently guide current work. Source skills never grant access. Filter against the current grant before context construction. A `candidate` never enters worker instructions as active guidance regardless of compatibility, role/audience, or freshness; an authorized lookup may return only an access-safe candidate reference for review. `active` guidance requires the complete predicate; `stale` and `revoked` versions cannot supply current instructions. Holdout/outcome material remains separate and every exposure is recorded. Keep canonical lifecycle values (`candidate`, `active`, `stale`, `revoked`) and current-validity values; do not add alternate global states. Preserve accepted specs, checklist, thresholds, reference answers, and the 003/009 shared contracts.

**Fixture boundary**: Since 022 is downstream of 015, early reuse fixtures may read frozen independently validated `active` or authority-revoked historical skill records with their validation/authority references as immutable fixture setup. This is not a production activation or revocation by 015. Feature 022 owns those authority transitions and the later live reuse path is exercised by milestone 004; 015 only consumes the existing lifecycle state.

**Scale/Scope**: One source-skill provider and compatibility predicate, with multiple individually scoped guidance items and recipe dependencies; bounded local fixtures exercise recurring source versions and grant changes. No broad skill-catalog search/index is introduced.

## Constitution Check

*Gate evaluated before design and re-evaluated after design.*

| Principle / gate | Design response | Result |
| --- | --- | --- |
| Honest analytical answer; process and evidence remain separate | Compatibility and recipe-use events are evidence about exposure and use, not proof that a claim is supported. Negative, stale, unknown, and denied cases remain explicit. | Pass |
| Analytical history cannot be rewritten | Every execution pins an immutable skill `RecordRef`; refresh adds a new version and linked events; old plans, exposure, outcomes, and pins remain. | Pass |
| Project memory is scoped and tested | Scope includes source, environment/tenant, grant, audience, meaning, and recipe privilege. Retrieval is access-filtered; every supplied source or outcome reference is recorded. | Pass |
| Skills do not grant privileges and changed assumptions invalidate affected guidance | The current grant is checked by the provider; mismatched required dimensions block current use; stale scope follows declared dependencies. | Pass |
| Features have a producer, consumer, viewer path, and injected failure | 014 creates the candidate, 015's extension supplies/records use and commits refreshes, and later inquiry/trace paths consume the events. The focused acceptance fixture injects semantic drift, unused-column change, narrower grants, revocation, and non-executed recipes. | Pass |
| Existing graph ownership and shared interfaces stay frozen | 015 changes only its three graph outputs and its planning artifacts; calls 003/009 interfaces without editing their files. 022 alone transitions authority to `revoked`. | Pass |

No constitution exception or unresolved product decision is required. The post-design gate passes with the same constraints.

## Design decisions

1. `compareSourceSkillCompatibilityV1` in `skill-compatibility.ts` is the sole normative comparison of the source-skill dimensions. It returns a structured assessment with changed/unknown dimensions, impacted guidance IDs, access-safe reasons, and whether cadence/drift requires a profile check. A scheduling helper may decide that an expensive check is due, but it does not make a second compatibility decision; the complete predicate evaluates the result of that check.
2. Compare source identity, environment/tenant, declared role and audience, current grant, lifecycle, exact required schema/key/grain facts, relevant unit/timezone/category/metric definitions, source-version compatibility evidence, watermark/lag freshness, and each recipe's required operations. An unknown required value, unvalidated version transition, expired freshness bound, or privilege excess cannot yield current-use compatibility. Fixtures cover identity/environment drift, a source-version transition with matching evidence and a missing/unvalidated transition, plus unknown required facts.
3. Each guidance item and recipe declares the exact semantic fact references it depends on. Compare these bindings rather than invalidating against a whole-schema fingerprint. A changed or unknown referenced fact stales only dependent guidance; an unused added column leaves unrelated guidance usable.
4. The provider performs source identity, lifecycle, grant, relevant metadata, version, and cheap freshness checks on each orientation. It requests the declared bounded profile only when cadence is due or a recorded drift trigger requires it. Tests assert that no profile runs before cadence when no drift is recorded, that a due cadence or relevant drift requests one, and that a pending profile never grants current use. A pass is bound to the exact source version and policy/check revision.
5. The provider is exercised through the ordinary 003 production inquiry-orientation caller and registered extension loader. It returns active instructions only when the exact skill version is `active` and the one compatibility predicate allows current use. A `candidate` may resolve to an access-safe review reference when the grant permits, but never to an entrypoint, reference content, or recipe in worker instructions; `stale` and `revoked` never supply current instructions. It appends a supplied-reference/version event; it records a recipe as executed only after the controlled recipe execution callback reports that exact recipe. Shared study/holdout summaries remain distinct and create an exposure record when supplied through the registered caller.
6. Refresh uses 009 `commitProjectMemory` with stable commit IDs and expected manifest revision. It writes a new immutable version and append-only changed-fact/staleness events. 015 may create a `candidate` replacement and offer an already validated, active compatible replacement; it never activates a candidate or changes a `revoked` version. 022 remains the authority for activation/revocation.
7. The module exports the frozen 003 `analyticsExtensionV1` shape with extension ID `source-reuse` and dependency `source-discovery`. No controller/CLI registry edits or custom persistence mechanism are introduced.

## Project Structure

### Documentation (this feature)

```text
specs/015-reuse-source-skill/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
│   └── source-skill-reuse.md
├── tasks.md
├── execution.json
└── verification.md
```

### Source Code (repository root)

```text
src/analytics/
├── skill-compatibility.ts       # Sole normative predicate and scoped comparison inputs/results
├── skill-refresh.ts             # Extension provider, orientation/use logging and immutable refresh
└── acceptance/
    └── 015.test.ts              # Synthetic feature acceptance and injected drift/access cases
```

**Structure Decision**: Use the graph's TypeScript analytics facade and feature-local acceptance test. Feature 015 does not add fixtures, schemas, commands, indexes, runner behavior, or shared files outside its graph outputs.

## Complexity Tracking

No constitution violations or added project layers require justification.
