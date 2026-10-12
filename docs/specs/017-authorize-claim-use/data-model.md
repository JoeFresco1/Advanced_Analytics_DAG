# Data Model: Claim Use Authorization and Decisions

The records below are feature-owned V1 payloads stored in the 009 project sidecar. The 009 record envelope supplies stable identity, revision, digest, creation actor, and commit linkage. References below use the frozen `RecordRefV1` form (record ID, revision, SHA-256 digest); paths are never identities. Unknown required bindings remain unresolved and cannot support authorization.

## UseAuthorizationPolicyV1

An immutable, project-owner-registered 017 policy defining when and by whom a use may be authorized. Registration is an owner-authenticated extension command and creates a new 009 project record.

| Field | Meaning and validation |
| --- | --- |
| `schemaVersion` | Literal `1`; unknown fields rejected. |
| `policyId`, `revision` | Stable policy identity and positive revision; edits create a new revision. |
| `ownerRef` | Configured project owner principal/record; not supplied by the worker. |
| `evidencePolicyRef` | Exact 010 policy revision for method checks and supported-presentation eligibility. |
| `effectiveScope` | Source, Claim kind, population/period, audience and action scope to which this rule applies. |
| `triggers` | Policy-owned event predicates: consequential decision link, actual external-publication request, or material use/audience/scope change. These consequential triggers require a configured authority route; routine internal return may have no gate. |
| `authorityRoute` | Configured principal/role refs permitted to make final use decisions, with effective period/revocation identity. The route must be present for consequential/publication triggers; absence cannot authorize the use. |
| `requiredReview` | Required reviewer category and distinct-actor conditions; may distinguish worker self-review, separate-agent review and human review. |
| `requiredRationale` | Named required rationale and uncertainty fields; blank/omitted substantive rationale cannot authorize. |
| `riskAcceptance` | Whether a specified use/evidence state may proceed by exception, which uncertainty must be named, and which configured authority can accept it. |
| `policyDigest` | Digest of canonical policy contents in the 009 record envelope; not a caller-supplied substitute for the content. |

The policy is not a grant and cannot expand source access. A policy change creates a new ref and stales assessments using the old policy when the change is material.

## UseRequestV1

A request for one use and scope. It includes the requested use kind (`routine_internal`, `consequential_decision`, or `external_publication`), named audience/action, Claim ref, and request actor. Use kind is a scope dimension, not an authorization state. A later use or wider audience is a new request.

A `trace_seen` event can record the principal, assessment ref, exact trace refs presented, and event time. It records presentation/access only. It has no authorization outcome or rationale by implication.

## UseAssessmentV1

An immutable evaluation of a single request under exact evidence and policy revisions.

| Field group | Required content |
| --- | --- |
| Identity | Stable assessment ID/ref, positive revision and predecessor assessment ref for reevaluation. |
| Request | Exact Claim ref, intended-use kind, action, audience, scope, trigger event refs and requesting actor. |
| Evidence | Exact Claim revision; all supporting and challenging Result refs; applicable Check Result refs and 010 eligibility ref; exact locked Plan ref and `plan_interpretation`; current source/artifact refs relevant to the Claim; linked Review refs. Empty or missing required evidence is explicitly represented as missing coverage/reasons, never as a pass. |
| Policy | Exact 010 evidence-policy ref, exact 017 use-policy ref, effective scope, configured authority route ref and its current validity. |
| Decision | Canonical `authorization_state`, attributed decision actor/ref, rationale, accepted uncertainty, unresolved issues, and decision time where present. |
| History | Prior assessment link, trace-seen event refs, staleness/revocation event refs, current-validity view, and creation/commit refs. |

The state is one of `not_assessed`, `pending_review`, `authorized`, `risk_accepted`, `denied`, `stale`, or `revoked`. `not_assessed` is the pre-request view. An incomplete requested gate remains `pending_review`; only a complete explicit negative configured-authority decision is `denied`. Material change yields `stale`; authority withdrawal yields `revoked`. These are separate facts from Claim evidence state and inquiry outcome.

### Exact binding and reevaluation

The evaluation identity includes Claim, Result, Check, Review, Plan/exposure, evidence policy, use policy, source/artifact, intended-use, audience and scope refs/values. It must compare each required dimension; unknown or missing evidence cannot compare equal. Changing any material bound dimension appends an invalidation event before new judgment and derives current state `stale`. It does not mutate the original assessment or Decision. Reassessment creates a new linked record. Revoking the configured authority appends a revocation event; affected current assessments derive `revoked` while the old affirmative record remains visible.

## ReviewRecordV1

An append-only record bound to one Use Assessment and exact presented trace refs.

| Field | Meaning |
| --- | --- |
| `reviewId`, `assessmentRef` | Stable review identity and exact subject assessment. |
| `actorRef`, `actorType` | Authenticated principal plus `worker`, `separate_agent`, or `human` category. |
| `authorityMatchRef` | Matching configured role/authority record, or explicit absence. Reviewer category alone grants no authority. |
| `traceRefs`, `tracePresentedAt` | Exact evidence trace presented to the reviewer and presentation time. A `trace_seen` event is separately linked. |
| `rationale`, `unresolvedIssues` | Substantive reason and remaining issues. Missing required rationale keeps the gate pending. |
| `outcome` | Attributed review recommendation/decision input; the controller validates authority and exact refs without attributing semantic truth to itself. |
| `createdAt` | Review record time; corrections create a new record. |

A separate-agent review stays distinguishable from worker self-review. It is not labeled independent empirical evidence; the review record preserves the actor and available context.

## DecisionV1

A lightweight immutable pointer to an action selected by a person. It is not a Claim and does not execute the action.

| Field | Meaning and validation |
| --- | --- |
| `schemaVersion` | Literal `1`; unknown fields rejected. |
| `decisionId` | Stable ID in the 009 record envelope. |
| `action` | Nonempty selected action. |
| `decidingActorRef` | Authenticated actor who selected it. |
| `claimRefs` | One or more exact Claim revision refs; no mutable latest pointer. |
| `useAssessmentRef` | Exact current `authorized` or policy-permitted `risk_accepted` assessment for the same Claim, use, audience and scope. |
| `constraints`, `rationale` | Applied limits and reason; risk acceptance includes accepted uncertainty. |
| `decidedAt` | Decision timestamp. |
| `externalActionRef` | Required external system/action pointer; missing value prevents finalization. |

A later evidence change, use change, policy revision or authority revocation preserves the Decision as historical and flags its current use relationship for reassessment through 016/017. Claim evidence state and plan exposure do not change.

## Relationships

- One Use Policy can govern many use assessments, but each assessment pins one exact policy revision.
- One Claim may have separate assessments for different uses, audiences or scopes.
- Each assessment pins its evidence refs and may have multiple linked review records and trace-seen events.
- Reassessment links to its predecessor; stale, denied and revoked history is retained.
- A Decision points to exact Claim revision(s) and one exact current Use Assessment; it creates no reverse evidence inference.
- 010 remains authoritative for check states/eligibility, 016 for dependency invalidation, 003 for actor and extension context, and 009 for committed history.