# Data Model: Skill Capture

All project-memory records carry the common immutable envelope and digest defined by `docs/build/interfaces.md`; commits use feature 009. Evidence links are stable `EvidenceRef`s (record ID, revision, digest), not paths. These feature records do not introduce lifecycle values outside the canonical vocabulary.

## CaptureCheckpointV1

Represents one automatic bounded evaluation point.

- `checkpointId`, `inquiryRef`, `kind` (`evidence_committed` or `feedback_received`), and occurrence time.
- `policyRef` and frozen `CaptureAllowanceV1` reference/limits supplied by the controller.
- `signalRefs` and evidence/feedback refs considered; `processedCount`; typed `dispositions`.
- `termination`: `complete`, `work_limit`, `time_limit`, or `deferred`; remaining budget and reason.
- Producer refs: the 003 committed inquiry/evidence manifest, 020 answer refs and retrieval/exposure events, the 003 terminal-intent/user-request ref for the analyst's next ordinary delegated question when it contains feedback, and 015 source-reuse refs for 014 candidate source packages. Feedback about an already completed inquiry is linked to its target answer by an explicit or exactly surfaced answer ref.
- Stable event/commit identity for idempotent replay.

The checkpoint never consumes capacity reserved for required finalization. Exceeding its allowance ends the checkpoint and persists a bounded reason; it does not retry.

## CaptureSignalV1

An attributed record of a possible reuse trigger. It binds a signal to evidence and distinguishes an eligible trigger from a suppression case.

- Shared fields: stable signal ID, inquiry ID, actor/provenance, time, typed trigger, scope, supporting evidence refs, and policy version.
- `method_endorsement`: explicit feedback in the 003 terminal-intent/user-request ref for a follow-on delegated question, matched to the already completed 020 answer by explicit ref or exact ref in its retrieval/exposure event, endorsing the method or rule and its referenced procedure/definition. Answer-only praise is not this type and is routed to answer annotation through the 020 facade; an ambiguous target cannot produce a method candidate.
- `repeated_validated_work`: a procedure identity, references to at least two distinct completed delegated inquiry/answer records available through 020, and validation refs for those uses.
- `costly_discovery`: committed 014 candidate source-package/discovery refs exposed through the 015 facade and the predeclared threshold kind/value and observed query count or elapsed time that crossed it.
- `validated_correction`: corrected fact/procedure, validation refs, and a later independent reuse ref linked through completed inquiry/answer or source-reuse records.
- Negative/filtered cases carry a reason such as `answer_only_praise`, `unvalidated`, `no_threshold_crossing`, `no_later_reuse`, `scope_unknown`, or `budget_exhausted`; negative case labels remain in the evaluator record.

Significance, model confidence, file count, and generic praise are not eligible signal variants.

## SkillCandidateProposalV1

An immutable proposal, always in canonical lifecycle state `candidate` until 022 makes a separate decision.

- Stable candidate ID/revision and prior/superseded candidate refs where revised.
- `triggerRef`, typed `skillType` (`source`, `method`, `business_rule`), candidate content/reference refs, and exact supporting signal/evidence refs.
- Intended audience and scope predicates for source identity, environment/tenant, role/grant, definition, period/window, and procedure inputs/outputs/assumptions as applicable.
- Expected reuse rationale, validation gaps, success/failure examples and validation evidence refs.
- Optional exact conflicting owner-definition ref and configured authority ref, pending reason, and candidate-only status.
- Separate outcome/reference refs with exposure provenance. Study-specific findings cannot be flattened into general candidate instructions.
- Dedup identity, prior candidate/signal refs, and decision rationale.

An incomplete or unknown scope stays explicitly unknown or is conservatively narrowed. Candidate content cannot add permissions or broaden an audience beyond the supporting grants.

## CaptureDecisionV1

The durable result for a signal/checkpoint. Capture disposition (`proposed`, `answer_annotation`, `suppressed`, `deferred`) is an event outcome, not a skill lifecycle state.

- Signal/checkpoint ref, disposition, reason, policy and evidence refs.
- Optional answer-card annotation ref, candidate ref, prior candidate/revision ref, or conflict-owner ref.
- Budget consumed/remaining and commit identity.

## Relationships and transitions

`CaptureCheckpointV1` considers committed inquiry evidence, bounded completed question/answer records, and feedback carried by the terminal-intent/user-request ref for the analyst's next ordinary delegated question. 003 dispatches the feedback checkpoint through dependency-ordered `beforeFinalize` after 020's answer finalizer commits the follow-on answer card; `afterEvidenceCommit` handles newly committed evidence. A feedback signal about an earlier inquiry must identify its target 020 answer through an explicit ref or the exact card ref in the 020 retrieval/exposure event; ambiguous feedback cannot produce a method candidate. A positive signal may produce a `SkillCandidateProposalV1`; an identical one links to the existing proposal with a suppression reason. Changed procedure content makes a new linked revision. Answer praise can annotate an `AnswerCardV1` through the 020 facade but cannot itself transition into a skill. Candidate source packages are read through the 015 facade from 014 records. Candidate state remains `candidate`; no active/stale/revoked transition is owned here. Any loaded prior outcome keeps its exposure linkage, and a candidate never rewrites the outcome's scope or validity.
