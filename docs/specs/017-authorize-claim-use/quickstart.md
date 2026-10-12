# Quickstart: Claim Use Authorization Acceptance

Use only synthetic/local records. This guide is for implementation after prerequisites 003, 009, 010 and 016 are present. It does not start the analytics runner, access external data, use evaluator reference answers, or contact a real decision system.

## Prerequisites

- Node.js 20+ and installed repository pnpm dependencies.
- The fixed 003 extension registry and actor/context services.
- 009 committed project-memory persistence and recovery/CAS APIs.
- 010 exact Check Result and supported-presentation eligibility records.
- 016 current-validity and dependency-invalidation seam.

## Focused runnable checks

Run the provenance and authority gate directly:

```powershell
pnpm exec node --import tsx --test --test-name-pattern "worker versus independent review attribution and configured authority gate" src/analytics/acceptance/017.test.ts
```

Run all 017 use and Decision fixtures:

```powershell
pnpm exec node --import tsx --test src/analytics/acceptance/017.test.ts
```

The focused fixture must demonstrate:

1. A descriptive inconclusive Claim can return internally with its uncertainty and limitations; it does not require a human pause solely because it is inconclusive.
2. A consequential request is tied to exact Claim, Result, Check, Review, Plan/exposure, 010 evidence policy, 017 use policy, source/artifact and scope refs. Missing or stale evidence cannot authorize it.
3. The named review-provenance test records worker self-review and separate-agent review as distinct actors. A separate agent is not thereby an approving authority or an independent empirical replication. Only the configured authority can issue the policy decision.
4. Opening/presenting the trace records trace presentation only. Empty rationale remains `pending_review`; explicit configured-owner rejection becomes `denied`.
5. Supported current evidence can be `authorized`; policy-permitted negative/conflicting evidence can proceed only through a use-scoped `risk_accepted` record naming actor, rationale and uncertainty. Claim state and post-observation Plan label remain unchanged.
6. Comparable current opposing Results produce an attributed `conflicting` Claim reassessment; different populations stay separate scoped Claims.
7. Changing a bound Claim, Result, Check, Plan, policy, source/artifact or use scope marks the previous assessment stale. Authority revocation marks affected current assessments revoked. A new assessment links to prior history.
8. Saving an internal report does not claim external-publication authorization; a real publication request gets its own use assessment.
9. A Decision points to exact Claim and current authorization refs, records action/actor/time/external pointer/constraints, and cannot finalize when a required field is missing. Later staleness does not rewrite it.
10. A seeded 009 commit-acknowledgement failure and same-ID recovery produce no visible partial assessment or duplicate authorization.

## Repository checks

After the fixture passes, run the build and repository test commands:

```powershell
pnpm run build
pnpm test
```

Record actual command outcomes and per-requirement fixture evidence in `specs/017-authorize-claim-use/verification.md`. These checks validate implementation wiring and fixture behavior; they do not establish that an interpretation is semantically true or that a local worker is strongly isolated. Do not change any accepted policy threshold, checklist, or reference answer to obtain a pass.