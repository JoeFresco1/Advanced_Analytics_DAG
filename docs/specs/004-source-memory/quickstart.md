# Quickstart: Source Memory Inquiry

This guide describes the post-implementation synthetic/local acceptance
path. It is not an implementation transcript; no product test is run during
the task-plan stage.

## Prerequisites

- The graph dependencies 003, 014, 015, and 022 are implemented and expose
  their frozen analytics providers. Feature 011's answer/trace handler is
  available through the normal viewer paths.
- The local CLI has an authenticated principal with the synthetic fixture's
  declared metadata and aggregate grants. Project configuration names the
  source owner who performs any required 022 review.
- The source fixture, assertion data, and reference evaluator are synthetic
  local data. The evaluator answer key is never included in worker context.
- The local model harness is configured for `gpt-6-luna` and `xhigh`; its
  access guarantees are described as workflow-level if the worker shell can
  read fixture files directly.

## First live inquiry: discover and validate

1. Build the local CLI after implementation:

   ```powershell
   pnpm build
   ```

2. Start a normal inquiry on the previously registered synthetic source:

   ```powershell
   dag analytics ask --project <fixture-project> --question "Summarize the declared synthetic usage metric by plan." --source <source-id> --model gpt-6-luna --reasoning xhigh
   ```

   During this ordinary ask, 014's registered `orient` hook finds no
   compatible active guide and calls the same grant- and budget-bounded
   discovery implementation used by the registered `source discover`
   command. Discovery commits the exact source version/window, scope, bounded
   query evidence, assertion provenance, and candidate package. It returns
   only the candidate's safe reference to orientation; candidate contents are
   not supplied as active guidance, and the hook does not activate it.

3. Independently replay a bounded recipe under the intended grant and retain
   its exact execution ref. Then invoke 022's registered `skills review`
   command through 003's normal typed command registry, using
   the candidate's exact ref, configured owner, approval/reason, and current
   project revision. The provider commits an active version only when its
   independent validation and conflict checks pass. A failed replay, unknown
   definition, unresolved conflict, or missing/rejected owner decision leaves
   the package `candidate`. The live path does not seed an active record.

4. Inspect the first inquiry's answer and claim trace through the normal
   answer/trace routes:

   ```powershell
   dag analytics answer show --inquiry <first-inquiry-id> --open
   dag analytics trace --claim <first-claim-id>
   ```

   Confirm that source and skill records have stable IDs/revisions/digests,
   and that candidate creation, the independent replay, the 022 decision,
   authority, and any limitations are separately visible through the existing
   011 handler.

## Second live inquiry: consume the exact validated version

Only after the 022 provider has committed the validated active version, start
a second ordinary ask on the same compatible synthetic source, in a separate
inquiry. Feature 015's registered `orient` hook performs the current
compatibility assessment and supplies the exact compatible version:

```powershell
dag analytics ask --project <fixture-project> --question "Which plan has the highest declared synthetic usage rate?" --source <source-id> --model gpt-6-luna --reasoning xhigh
dag analytics answer show --inquiry <second-inquiry-id> --open
dag analytics trace --claim <second-claim-id>
```

In 011's existing answer/trace inspector verify the exact active skill
revision/digest and source version; the supplied reference IDs; the separately
recorded recipe execution; current grant/audience; provenance for every
assertion; exposure status; and fewer discovery steps than the cold inquiry.
The acceptance test reads
`GET /api/analytics/inquiries/{inquiryId}/answer` and
`GET /api/analytics/claims/{claimId}/trace` behind the `answer show --open`
and `trace --claim` routes and follows the returned committed evidence links
to committed source, skill/status, validation, authority, supplied-reference,
executed-recipe, and exposure refs under current viewer grants. This is 011's
generic linked-provenance projection; no 004 viewer route or feature-specific
DTO is added. Required analytical checks remain present.

## Failure fixtures

The focused feature suite mutates only local synthetic fixtures and checks:

- same-schema, different-meaning environments and tenant/role mismatch;
- changed key/grain, unit, timezone/category meaning, stale watermark, and a
  failed join on a later window;
- an unused-column addition that leaves unrelated guidance active;
- configured-owner `skills revoke` through provider 022, preserving old pins
  and excluding current use;
- untrusted comments, sensitive metadata/raw-row sentinels, prior-outcome
  content, over-budget discovery, and a recipe requiring a wider grant.

Run the feature acceptance through the repository suite:

```powershell
pnpm test
```

The test passes only when no required case is skipped or TODO and all listed
criteria remain unchanged. A compatibility failure must leave the inquiry's
locked source and skill refs intact and produce an honest unresolved outcome
or limitation.
