# Data Model: Skill Activation

All records below are immutable `schemaVersion: 1` project-memory records
with stable IDs, revisions, content digests, actor/provenance references, and
committed-manifest visibility. They are feature-owned payloads published by
009. The project catalog remains a rebuildable projection. Exact shared
record references and commit/recovery outcomes follow `docs/build/interfaces.md`.

## Skill version

| Field | Meaning and validation |
| --- | --- |
| `skillId` | Stable identity across versions. |
| `versionRef` | Stable record ID, revision, and digest for this immutable version. |
| `priorVersionRef` | Optional predecessor; refresh/change creates a new version and preserves the predecessor. |
| `type` | Source, method, or business rule, as declared by the candidate. |
| `lifecycleState` | Exactly `candidate`, `active`, `stale`, or `revoked`. No extra lifecycle values. |
| `candidateRef` / `candidateEvidenceRefs` | Exact committed 014 source proposal or 021 method/rule proposal and its motivating evidence. Missing or noncommitted evidence blocks activation. |
| `contentRef` | Immutable entrypoint, references, examples/recipes, assumptions, failure cases, and invalidation conditions. A source version also binds exact source/environment/version/role scope and assertion provenance. The body contains no credentials or raw sensitive records. |
| `scopePredicateRef` | Versioned declared predicate for method/rule skills. Every required dimension is structured and known before a match can return true. Source skills use 015's predicate. |
| `audience` | Principal/role references and effective scope; activation cannot grant source rights. |
| `authorityRef` | Configured owner or versioned policy that authorizes this exact scope. Candidate text cannot appoint authority. |
| `activationDecisionRef` | Exact decision, reason, policy version, validation refs, conflict assessment, and authority basis. Required for `active`. |
| `outcomeRefs` | Optional study-specific results kept separately from general skill assertions, with scope and exposure provenance. A single Result cannot become an unscoped rule. |
| `createdAt` | Version creation time; corrections produce a new record. |

## Scope predicate

`ScopePredicateV1` identifies its own stable ID/revision and the scope
dimensions it requires. Each predicate names structured fields and a
deterministic equality or containment condition over known source, role,
population, metric/denominator/definition, period, intended use, and method
assumptions as applicable. It records the author and validating policy/evidence
references. An absent, unknown, or mismatched required dimension means the
skill is not supplied as current guidance. Predicate changes create a new
skill version. Source candidates call the feature 015 compatibility
predicate rather than copying or weakening it.

## Source assertion provenance and independent replay

Each source assertion retains its 014 provenance class (`declared`,
`observed`, `tested_on_version/window`, or `inferred`), exact source version
and scope, evidence reference, and validation time. Activation does not
upgrade provenance by wording or worker confidence. Unknown definitions stay
unknown; unresolved owner conflicts stay linked to the configured authority.
Such assertions may remain visible in a source package only as explicitly
unresolved, nonauthoritative guidance. A required unresolved definition,
conflict, missing source version, or unknown scope prevents activation of the
affected claim as a fact and keeps the affected candidate decision visible.

A source activation's `independentTestRefs` bind a controller/policy-owned
replay of the candidate's bounded recipe to its exact source version and
intended current grant. The record includes the validator and policy
identity/version, execution reference, tested assertions, access grant,
candidate-author relation, observed result, and limits. A replay created by
the candidate author as its own validation does not satisfy this gate. The
replay verifies only its declared source facts and recipe properties; it does
not prove untested business meaning.

## Activation decision

| Field | Meaning and validation |
| --- | --- |
| `decisionRef` | Immutable stable record reference. |
| `candidateRef`, `skillVersionRef` | Exact proposal and version under consideration. |
| `candidateEvidenceRefs`, `type`, `scopePredicateRef`, `audience` | Required FR-001 activation facts. |
| `policyRef` | Current preapproved activation or review policy. Missing/stale policy blocks activation. |
| `independentTestRefs` | Exact test evidence. For a method, bind the policy-owned validator identity/version, exact candidate/example subject, independent fixture or eligible committed execution, outcome, and candidate-author relation. For a source skill, additionally bind the independent bounded recipe replay to its exact source version and intended grant. Worker-authored self-tests cannot satisfy this field. |
| `conflictAssessmentRef` | Exact compared authoritative definitions and conflict outcome/reason. Conflict or unresolved comparison keeps the affected authoritative assertion a candidate. |
| `authorityRef`, `authorityDecisionRef` | Existing configured owner/policy and concrete approval or review. A source activation may record only an already configured owner or a preapproved policy that exactly covers its scope; a method's low-risk auto policy does not imply source permission. The authenticated principal is checked against project configuration. |
| `decisionReason` | Human-readable reason for automatic activation, owner approval, or continued candidate status. |
| `recordedAt` | Event timestamp; retries reuse stable commit/event IDs under 009 semantics. |

## Lifecycle event

An append-only event binds the exact skill version, prior state, new state,
actor, reason, policy/authority, changed assumptions, and timestamp. State
transitions are:

```text
candidate --eligible independent validation and policy/authority--> active
active --bound assumption/source-unit/access-role change-----------> stale
active --configured authority withdrawal---------------------------> revoked
stale --configured authority withdrawal----------------------------> revoked
```

A held, failed, or rejected activation remains `candidate` with a recorded
reason. `stale` guidance can only be refreshed as a new candidate/version;
the old version stays stale. `revoked` is terminal for that version. A new
candidate may supersede it, but no transition rewrites the old record.

## Skill use and exposure

| Field | Meaning and validation |
| --- | --- |
| `inquiryRef` | Later inquiry receiving the guidance. |
| `suppliedSkillRefs`, `suppliedReferenceRefs` | Exact active skill/reference versions placed in context, plus scope/access decision. |
| `executedExampleRefs` | Empty unless execution evidence proves an example/recipe ran. |
| `rejectedOrUnusedReason` | Optional attributed explanation; supplied-only does not imply executed use. |
| `outcomeExposureRefs` | Prior Result/evaluation refs, provenance and exposure records; kept distinct from reusable instructions. |
| `principalRef`, `scopeMatchRef`, `grantRefs` | Authenticated user/inquiry context and current permission/scope assessment. |
| `recordedAt` | Append-only usage event time. |

## Persistence and concurrency

Candidate decisions, activation events, invalidations, revocations, and use
records use 009 project commits. Stable commit IDs make retries idempotent;
stale manifest revisions return a conflict and require a fresh attributed
decision. Incomplete staging is never reader-visible. Current state is a
projection of committed lifecycle events; it is not written into the neutral
run file or inferred from a search index.
