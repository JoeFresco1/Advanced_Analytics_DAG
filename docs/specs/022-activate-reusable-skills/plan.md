# Implementation Plan: Activate and Reuse Learned Skills

**Branch**: `022-activate-reusable-skills` | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification at `specs/022-activate-reusable-skills/spec.md`.

## Summary

Implement source, method, and business-rule activation in
`src/analytics/skill-activation.ts`, with deterministic synthetic acceptance
fixtures in `src/analytics/acceptance/022.test.ts`. The provider consumes
committed source candidates from 014 and method/rule candidates from 021,
uses the typed extension registry owned by 003, and publishes through 009's
append-only project-memory primitive. It records immutable
candidate/active/stale/revoked version decisions. A method auto-activates only
under the preapproved low-risk project policy after independent
policy-owned validation and a clear conflict check; authoritative changes
route to the configured owner. Source activation independently replays the
bounded recipe under its exact intended grant, verifies assertion provenance,
source facts, scope, and conflicts, and records only an already configured
owner or preapproved policy as authority. Unknown or conflicting business
definitions remain explicit and nonauthoritative. Orientation supplies only
current, accessible versions whose declared scope predicate matches, using
015's source compatibility predicate. Supplied references, executed recipes,
and prior-outcome exposure are recorded separately. Feature 004 is the later
live source-reuse consumer, not a prerequisite for 022's candidate-to-active
path.

## Technical Context

**Language/Version**: TypeScript on Node.js 20 or newer, following the frozen
analytics runtime in `docs/build/ARCHITECTURE.md`.

**Primary Dependencies**: Existing TypeScript runtime and the frozen
`AnalyticsExtensionV1`/`AnalyticsCommandV1` seam in `docs/build/interfaces.md`;
the graph prerequisites 003, 014, 015, 020, and 021; and 009's project-memory
publication. No package or dependency change is needed. Method/rule
validation uses the project policy and committed synthetic fixtures or an
eligible prior committed execution. Source activation consumes the committed
014 candidate and its bounded recipe, then uses the 015 source-compatibility
predicate. Feature 004 consumes the resulting active source version later.

**Storage**: Immutable versioned project-memory records written with 009's
`commitProjectMemory` and recoverable with `recoverProjectMemory`. Indexes are
projections only. No analytics state is written to `dag.run.json`.

**Testing**: `src/analytics/acceptance/022.test.ts`; `pnpm build` for staged
compilation and `pnpm test` for the complete existing Node test suite and
build. Fixtures are synthetic/local and use the already frozen fixture
registry. The accepted feature spec, policy thresholds, fixture labels, and
reference answers remain unchanged.

**Target Platform**: Local analytics CLI/controller and scoped inquiry
orientation, with user inspection through committed project records and the
existing answer/trace path. The 022 source-candidate activation fixture runs
before 004; 004 later demonstrates the first and second live source inquiries
using this provider.

**Project Type**: TypeScript analytics controller extension and acceptance
slice.

**Performance Goals**: Activation and scope checks stay bounded by the
candidate's declared evidence, policy validator, and current committed skill
set; orientation returns only the bounded eligible guidance set. This feature
does not add model calls or an unbounded validator loop.

**Constraints**: Worker-authored tests cannot establish activation. Each
activation record must bind candidate evidence, type, version, scope
predicate, audience, authority, independent test, conflict check, and reason.
Method auto-activation is limited to preapproved, low-risk, project-scoped
methods that do not change access, required evidence, or release policy.
Source recipes require an independent bounded replay under the intended
grant; no new policy permission or owner identity is inferred. A source skill
may retain unknown or conflicting definition notes with their provenance,
but cannot activate them as authoritative facts. New or changed authoritative
definitions and expanded audience/access/release policy wait for the
configured owner. A skill never grants access. Supplied guidance is not proof
of comprehension or use. Prior outcomes retain provenance and exposure.

**Scale/Scope**: One project memory namespace, a small set of candidate and
active skill versions, a synthetic two-inquiry method-reuse demonstration,
and a pre-004 source candidate-to-active provider demonstration. Feature 004
later consumes the active source version in its live first/second inquiry
path. Cross-project sharing and a skill marketplace remain out of scope.

## Constitution Check

| Gate | Result | Design response |
| --- | --- | --- |
| Honest answer and distinct evidence, judgment, and process facts | Pass | Activation decisions depend on scoped evidence and policy checks, not task exit status or worker confidence. Negative, inconclusive, and failed checks remain records. |
| Append-only plans, outcomes, reviews, and history | Pass | Each skill version and decision is immutable; refresh creates a new version. Earlier inquiries keep the exact version they used. Project writes use 009's CAS and replay semantics. |
| Scoped and tested project memory | Pass | Candidate, active, stale, and revoked are the only lifecycle values. Every version has authority, audience, scope, validation provenance, and invalidation facts. Orientation records supplied and executed use separately. |
| Access and judgment attribution | Pass | The controller resolves authenticated principal and configured grants/owner. A worker cannot appoint an owner, broaden access, or publish policy. Owner and validator judgments remain attributed. |
| Bounded local guarantees and policy | Pass | Validators have a policy-owned identity/version and bounded local fixture inputs. A local shell is not called an isolation boundary. No automatic retry seeks a favorable method result. |
| End-to-end producer, consumer, inspection, and failure | Pass | 014 source discovery and 021 candidate capture feed 022 decisions; 003 registry dispatches commands/orientation; 009 persists decisions; a two-inquiry method fixture asserts exact-version reuse, while a source fixture proves candidate → independent replay → committed active version through the real provider before 004 consumes it; injected invalidation, access, unresolved-definition, outcome-exposure, and owner-review cases are observable. |

No constitution exception or added architectural component is required.

## Design Boundaries and Data Flow

1. Feature 014 creates source candidate packages and 021 creates/deduplicated
   method/rule candidates; neither activates a candidate. Feature 022 reads
   committed candidates and records its own activation, authority, validation,
   and usage events. Its dependency set is 003, 014, 015, 020, and 021; 004 is
   downstream and is not needed to build or verify this activation path.
2. A project policy version identifies the eligible skill types/scopes,
   independent validator identity and version, and configured owner routing.
   Only a preapproved low-risk project method can use automatic activation.
   A worker self-test is evidence attached to the candidate, never the
   independent policy result.
3. For source candidates, resolve the exact candidate through 014's existing
   source-discovery facade and independently replay its bounded recipe under
   the intended, current grant and source version. Verify source facts,
   assertion provenance, scope, and conflicts. Use 015's compatibility
   predicate without reimplementing it. Record only a matching configured
   owner or already preapproved policy as authority; absence of such authority
   leaves the candidate in `candidate` and does not infer new permission.
   Unknown or conflicting business definitions remain explicitly unresolved
   and never become authoritative facts in the active version.
4. The method validator checks the candidate's scoped example against an independent
   fixture or prior committed execution. The candidate author did not create
   that check execution. The conflict check compares against current committed
   definitions. Either failed, inconclusive, missing, stale, or conflicting
   check leaves the version in `candidate` with its reason retained.
5. A new or changed authoritative definition, or an expanded audience, access
   rule, or release policy, requires the project-configured owner. A concrete
   approval already given by that owner is recorded once and satisfies the
   authority decision after required independent tests and conflict checks;
   it does not ask for duplicate approval.
6. Guidance orientation filters current status, audience, current principal
   grants, freshness, and the declared method/rule scope predicate before
   adding any reference to worker context. Source-skill reuse consumes 015's
   normative complete compatibility predicate rather than implementing a
   second one.
7. Orientation records exact supplied skill and reference versions. A separate
   use event records a recipe/example only when its execution is evidenced.
   Outcome references preserve study scope and exposure. A single-study result
   cannot be generalized as an authoritative skill claim.
8. Staleness affects only versions whose bound assumptions changed. Configured
   authority withdrawal sets the affected active version to `revoked`; a
   source-unit or access-role change sets affected guidance to `stale`. Neither
   event rewrites old use records or inquiries.

### Production caller and inspection path

The `skill-activation` provider exports `analyticsExtensionV1` from
`src/analytics/skill-activation.ts`, depends on the registered `skill-capture`
provider, and adds activation/review/revocation commands plus scoped guidance
orientation through 003's frozen registry. Candidate and decision events are
published using 009's project-memory primitive. Before 004, the synthetic
acceptance path invokes this provider with a committed 014 source candidate,
records the independent intended-grant recipe replay and configured authority,
commits an immutable active version, and verifies that provider orientation
can expose that exact version through 015's predicate. A separate method
two-inquiry fixture asserts the exact active method version is supplied and
used without a save/recreate prompt. Feature 004 then consumes the source
version in its live first/second inquiry demonstration. Committed version and
use records are inspectable alongside the inquiry's answer and trace. Tests
inject a self-authored-only method test, failed or overprivileged source
recipe, unresolved source definition, a changed source unit/role, unavailable
privilege, single-study generalization, prior evaluation exposure,
stale/revoked versions, and owner-review cases.

## Project Structure

### Documentation (this feature)

```text
specs/022-activate-reusable-skills/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── tasks.md
├── execution.json
├── verification.md
└── contracts/
    └── skill-activation-v1.md
```

### Source Code (repository root)

```text
src/analytics/
├── skill-activation.ts             # 022 provider, activation lifecycle and orientation
└── acceptance/
    └── 022.test.ts                 # scoped activation/review/reuse acceptance fixtures
```

`src/analytics/extensions.ts`, the CLI/controller, shared policies, fixture
registry/lock, and storage/publication implementation stay with their frozen
owners. 022 adds a provider through the existing allowlist and uses the
existing project-memory commit/recovery interface.

**Structure Decision**: Keep all feature-owned runtime behavior and test cases
in the two paths already assigned to feature 022 by `docs/build/graph.json`.
Use the plan directory only for this feature's planning artifacts.

## Complexity Tracking

No constitution violations or additional subsystem are proposed.
