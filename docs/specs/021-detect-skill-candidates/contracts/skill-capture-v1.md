# Skill Capture Extension Contract (Feature 021)

## Frozen extension seam

`src/analytics/skill-capture.ts` exports `analyticsExtensionV1: AnalyticsExtensionV1`, as owned by graph node 021. The shared type, loader, and dispatch remain owned by 003. The provider declares `schemaVersion: 1`, unique `extensionId: "skill-capture"`, and `dependsOn: ["answers", "source-reuse"]`. It registers no special user command: capture is automatic.

Use the frozen lifecycle hooks only:

- `afterEvidenceCommit(context, manifestRef)` evaluates newly committed meaningful-result signals.
- `beforeFinalize(context, terminalIntentRef)` evaluates feedback carried by the existing terminal-intent/user-request refs and ensures capture disposition records are durable before inquiry completion. An analyst's ordinary follow-on delegated question/request is the production source when it gives feedback about a completed answer. Dependency order runs 020's `answers` finalizer first, so 021 can read the follow-on answer card and associate the feedback with an explicit prior answer ref or the exact card ref in 020's existing retrieval/exposure event. An ambiguous target cannot produce a method candidate. 003 supplies the dispatch; no separate user command or callback is added.

The two hooks consume existing production records. `afterEvidenceCommit` reads the committed 003 inquiry/evidence refs. `beforeFinalize` reads the 020 answer refs, retrieval/exposure events, the follow-on question's terminal-intent/user-request refs, and a bounded set of completed delegated inquiry/answer records through the `answers` facade. A feedback signal about an earlier inquiry must resolve to that answer's exact ref from the request or event. The 015 `source-reuse` facade exposes committed candidate source packages produced by 014. The controller-provided checkpoint allowance bounds record/signal processing. This lets feedback, repeated-work, costly-discovery, and correction signals arise from completed inquiry records and existing facades.

All writes return committed project refs through the extension contract. Hook failures follow 003's required-hook behavior; on replay, stable event and commit IDs make writes idempotent. Capture does not alter a terminal outcome, check result, claim support, grant, policy, or finalization allowance.

## Typed signal contract

Each signal has an immutable ID, source/provenance, inquiry and policy refs, timestamp, typed trigger, exact scope, and evidence refs. Positive trigger variants are `method_endorsement`, `repeated_validated_work`, `costly_discovery`, and `validated_correction`. Each variant must include its direct evidence:

- Endorsement points to feedback in a follow-on 003 terminal-intent/user-request ref, the exact prior target answer ref from 020 (explicit or resolved from its retrieval/exposure event), and the endorsed committed procedure/definition. Praise of an answer alone routes to the answer-card annotation path in 020 and a visible non-capture reason.
- Repetition names the same typed procedure and at least two distinct completed inquiry/answer records obtained through 020, with successful validation evidence for each.
- Costly discovery carries a committed 014 source-package/discovery ref surfaced by 015, a predeclared query or elapsed-time threshold, and the observed value that crossed it.
- Correction carries the correction evidence, validation evidence, and later independent reuse record from completed inquiry/answer or source-reuse records.

No signal is positive solely because of significance, model confidence, user praise without method/rule endorsement, or file count.

## Candidate proposal and suppression

A proposal records trigger, evidence refs, one of `source`/`method`/`business_rule`, intended audience, evidence-bounded scope, expected reuse rationale, validation gaps, and dedup/revision links. A conflict includes the exact owner-curated definition and owner refs and remains a candidate for 022 review. The provider must not activate candidates, appoint authority, or expand access.

Identical typed procedure plus scope resolves to one candidate; repeat proposals emit a visible suppression/link decision. A changed procedure emits a linked candidate revision without overwriting its predecessor. Outcome-bearing material stays a separately scoped answer/evidence reference with provenance and exposure; it is not copied into generic guidance. A source role/period supported only once stays at that scope.

## Bounds and persistence

The checkpoint must consume only `CaptureAllowanceV1` and remaining budget supplied by the controller under a frozen policy version. It ends at the first declared work or time limit, emits a durable defer/suppression reason, and reserves required reporting/finalization capacity. It never retries for a positive result. The feature must not invent or tune evaluator labels or thresholds.

Persist candidates, answer annotations, duplicate/suppression decisions, and checkpoint outcomes with the 009 project-memory commit/recovery service. Use stable IDs and expected project revision; preserve prior revisions and exposure. The SQLite catalog is a projection and is not a write authority.

## Independent evaluation output

The feature-owned acceptance test consumes the frozen 48-signal cohort through the 18-bootstrap `src/analytics/evaluation/registry.ts` API. It pins/selects the fixture batch, records capture responses, resolves evaluator-only references only after each response, and adjudicates through `adjudicateResponseV1`; expected labels never enter capture context. The locked cohort contains eight positives for each of the four trigger types, eight negative answer-only praise signals, and eight negative unvalidated-significance signals. For the 48 signals, it reports proposal IDs, true-positive count, total proposal count, positive-signal count (32), precision, recall, and duplicate-proposal count. Missing labels or denominators fail. Required frozen gates remain precision ≥0.80 over at least 10 proposals, recall ≥0.80 over all 32 positives, and zero duplicate proposals. The evaluator's fixture lock, metric registry, labels, and thresholds remain read-only.
