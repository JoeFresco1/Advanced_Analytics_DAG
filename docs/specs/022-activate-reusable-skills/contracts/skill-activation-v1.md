# Skill Activation Provider Contract

This feature adds one provider in the graph-owned module
`src/analytics/skill-activation.ts`. It exports
`analyticsExtensionV1: AnalyticsExtensionV1` using the frozen type and
loader implemented by 003. It does not edit `extensions.ts`, the shared CLI,
controller, project store, fixture registry, or source compatibility owner.

## Provider registration

- `schemaVersion`: `1`.
- `extensionId`: `skill-activation`.
- `dependsOn`: `skill-capture` (the 021 provider). Registry dependency order
  makes committed candidates available before activation handling.
- Commands use the authenticated principal and project/run/inquiry service
  context supplied by 003. Authority is read from project configuration and
  cannot be supplied as a worker-selected ID or role.
- Required commands use the configured administration/owner grant. Missing,
  expired, too-narrow, or non-owner identity returns the existing typed access
  denial; it creates no activation decision.

## Commands

| Token path | Required inputs | Behavior |
| --- | --- | --- |
| `skills activate` | Exact candidate ID and expected project revision | For a method, evaluate the frozen low-risk auto policy, policy-owned independent validator, scope, and conflict assessment; auto-activate only when every preapproved condition passes. For a source candidate, resolve the exact 014 candidate, independently replay its bounded recipe under the exact intended current grant/source version, verify assertion provenance/facts/scope/conflicts using existing 014/015 facades, and record only an already configured owner or exactly applicable preapproved policy as authority. If required authority is absent, unresolved, or not configured, retain candidate status. This command cannot infer policy permission or bypass owner review. |
| `skills review` | Exact candidate ID, approve/reject decision, substantive rationale, expected project revision | Resolve the current configured owner from the authenticated principal. Record the authority decision once for that concrete version/scope. Approval activates only after independent validation and conflict checks pass; rejection leaves the version candidate with its reason. A concrete owner approval already recorded for the same decision is replayed without a duplicate prompt/event. |
| `skills revoke` | Exact active/stale skill version ID, withdrawal reason, expected project revision | Only the configured authority may withdraw. Set that version to `revoked`, block new supply, and preserve earlier use records. |

The registry supplies the exact argument schema and operation grant for each
command. Commands publish immutable decision/event records through
009's `commitProjectMemory`; they preserve 009's idempotency and
revision-conflict behavior. No command accepts arbitrary artifact paths,
policy content, validators, owner identities, or audience grants from the
worker.

## Inquiry lifecycle

The provider adds scoped orientation using the frozen `orient(context)` hook.
It filters committed versions by canonical current state, audience, present
grant, freshness, and the version's declared scope predicate before returning
bounded context additions. It emits exact supplied references and exposure
refs. It records executed examples only when an execution reference confirms
the recipe ran. Source-skill compatibility is delegated to 015's normative
predicate; the activation provider does not create a second source matcher.

For source activation, 014 remains the source-discovery and candidate
producer, while 015 remains the sole source-compatibility owner. The 022
provider independently replays the candidate recipe within its bounded
contract and intended grant, verifies only declared source facts and their
provenance, checks configured conflicts and exact scope, and retains unknown
or conflicting business definitions as unresolved/non-authoritative. The
replay cannot grant access or turn observed/inferred metadata into an owner
definition. A missing, expired, or too-narrow intended grant; failed,
inconclusive, or candidate-authored-only replay; unknown required scope; or
unresolved required conflict leaves the candidate in `candidate` with its
reason recorded.

During dependency-ordered pre-finalization, after 021 has recorded method/rule
candidate capture, 022 also consumes exact source candidates already
committed by 014. It evaluates methods against the preapproved automatic
method policy and source candidates against their independent replay and
already configured owner or policy authority. It writes a qualifying
activation decision through 009. No new source activation permission is
inferred from the method policy. Candidate tests remain bound to the exact
candidate/source version. A missing provider, validator error, unresolved
conflict, or failed test is retained as a candidate decision reason and never
retries until favorable. The committed active source version is available to
004's later live first/second-inquiry demonstration; 022 validates this
candidate-to-active path without depending on 004.

## Record and failure contract

Activation, owner review, conflict, independent test, lifecycle, supplied-use,
executed-use, and outcome-exposure records follow
`data-model.md`. Canonical lifecycle values are exactly the state vocabulary's
four values. Every activation cites candidate evidence, skill type/version,
scope predicate, audience, authority, independent test, conflict check, and
reason. Source activations additionally bind the independent recipe replay,
intended grant, source version, and assertion provenance. A required field or
exact reference missing at admission blocks the transition. Historical
inquiries retain their pinned version even after it becomes stale or revoked;
new orientation supplies neither as current guidance.
