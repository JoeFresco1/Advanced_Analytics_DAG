# Use Authorization V1 Contract

**Owner**: Feature 017 (`src/analytics/authorization.ts`, `src/analytics/decisions.ts`)  
**Consumers**: 003 extension registry/finalization, 005 recurrence-and-use integration, existing 011 answer/trace references  
**Dependencies**: 010 frozen evidence policy, 016 validity/invalidation, 009 append-only project persistence

This feature contract adds no shared registry, state vocabulary, CLI dispatcher, persistence mechanism, or viewer route. The 017 provider exports `analyticsExtensionV1` with `schemaVersion: 1`, `extensionId: use-authorization`, and `dependsOn: [recurrence]` through the frozen 003 allowlist.

## Policy-owned trigger and authority contract

`UseAuthorizationPolicyV1` is immutable and registered by the configured project owner. It pins the exact 010 evidence-policy ref and declares:

- Effective Claim, source, use, audience and scope applicability.
- Trigger rules for a consequential Decision link, an actual external-publication request, or a material use/audience/scope change.
- Configured authority principal/role refs and current validity.
- Required review actor category/distinct-actor conditions and substantive rationale fields.
- Whether a policy-scoped risk acceptance is permitted for specified evidence states and uses, and which uncertainty must be named.

The worker can request a use and provide attributed judgments. It cannot register or change triggers, appoint the authority, waive required checks, or supply a new effective policy revision. Consequential/publication triggers require a configured authority route; absence of that route fails closed. Use kind is not inferred from method mode. Saving an internal report alone is not an external-publication trigger, and off-platform copying remains outside observable use events.

## Commands and hook

Commands are registered by the extension; 003 retains ownership of dispatch and argument parsing:

| Command path | Required inputs | Behavior |
| --- | --- | --- |
| `use policy register` | Strict `UseAuthorizationPolicyV1` payload | Require the configured project-owner principal; validate scope, authority route and exact 010 evidence-policy ref; append a new immutable policy revision through 009. |
| `use assess` | Claim ID, intended use, audience and scope | Resolve current exact Claim/Result/Check/Review/Plan/policy refs; return a committed assessment and access-safe trace refs. Missing bindings never qualify. |
| `use review` | Assessment ID, rationale, explicit review outcome | Require authenticated actor, exact displayed refs, and configured authority for a final authorization/risk acceptance/denial. Persist actor type and unresolved issues. |
| `use status` | Assessment ID | Return current derived authorization state, predecessor, stale/revoked reasons and historical refs without changing them. |
| `decision record` | Assessment ID, selected action, external action ref, actor and constraints | Create a Decision pointer only for the exact current authorized or permitted risk-accepted use; reject missing fields and mismatched scope/Claim refs. |

The provider implements `beforeFinalize`. For a policy-triggered consequential action/publication, it returns a restriction unless a current exact `authorized` or permitted `risk_accepted` assessment is committed. This restriction does not turn a truthful internal uncertain answer into a failed inquiry or change canonical inquiry outcome.

## Record and transition contract

Use assessment references bind Claim, Results, Checks and 010 eligibility, Reviews, Plan/exposure, source/artifact, 010 evidence policy, 017 use policy, authority, intended use, audience and scope. References use the frozen stable ID/revision/digest envelope. The assessment also records missing evidence, unresolved review and trigger reasons explicitly.

Authorization values are exactly `not_assessed`, `pending_review`, `authorized`, `risk_accepted`, `denied`, `stale`, and `revoked`. Trace presentation or a `trace_seen` event does not change the state. Missing rationale or unresolved required review remains pending; an explicit negative configured-authority decision is denied. Ordinary authorization requires current policy-eligible evidence and all configured approvals. Negative or conflicting evidence cannot receive ordinary authorization. A policy-permitted exception requires a named actor, substantive reason, named uncertainty and exact use, while Claim evidence and Plan exposure remain unchanged.

Any material change to a bound Claim, Result, Check, Review, Plan/exposure, evidence/use policy, source/artifact, use, audience, authority or scope appends an invalidation event and derives the affected assessment as `stale`. Authority withdrawal derives affected current assessments as `revoked`. Reassessment creates a new assessment linked to the old one. Historical assessment and Decision payloads remain inspectable. 016 owns common dependency invalidation traversal; 017 consumes its events and handles its own exact-use bindings.

When two current, methodologically comparable Results point materially in opposing directions for one scoped Claim, the worker or named reviewer records a `conflicting` Claim reassessment with both Result refs and a reason. The controller validates exact references but does not claim semantic proof. Different populations/estimands remain separate Claims.

A Decision is immutable and contains action, actor, exact Claim revision refs, exact current Use Assessment ref, time, constraints/rationale and external action ref. It points to an organizational choice; it neither performs the action nor changes evidence state.

## Persistence, access and failure behavior

All domain records use 009 `commitProjectMemory` and recovery/CAS. No direct sidecar write or second store is allowed. Stable IDs make replay idempotent; incomplete/unacknowledged commits cannot be displayed as authorization. Current principal/role is resolved by 003; absent identity or authority grant fails closed. Record reads and trace refs remain subject to current access checks. An authority revocation is an append-only event with an access-safe historical pointer.

The fixture injects a failed persistence acknowledgement and then retries recovery with the same IDs. It must preserve the pending/previous state until the commit is confirmed and must produce one logical record, never duplicate an authorization or silently promote an orphan.