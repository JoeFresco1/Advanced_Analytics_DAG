# Quickstart: Verify Skill Activation and Later Use

This guide describes planned local verification. It uses only the feature's
synthetic/local acceptance fixtures. It does not invoke the analytics runner
or use external data.

## Prerequisites

- The 022 implementation and graph prerequisites 003, 014, 015, 020, and 021
  are present in the checkout; 009's project-memory service is available
  through its frozen interface. Feature 004 is downstream and is not required
  for this feature's source candidate-to-active acceptance path.
- Project policy fixtures define the configured owner and the preapproved
  low-risk method profile; 18-bootstrap supplies the locked synthetic fixture
  registry. No reference labels or policy thresholds are edited.
- Node.js 20 or newer and the repository's pnpm dependencies are installed.

## Run feature acceptance

```powershell
pnpm build
pnpm test
```

Expected: `pnpm build` type-checks the provider and test module. `pnpm test`
runs the full Node acceptance suite, including
`src/analytics/acceptance/022.test.ts`. The 022 cases report evidence for
worker-authored self-test rejection, independent eligible method activation,
source recipe replay under its intended grant, assertion provenance,
owner-gated authoritative changes, exact scope/access selection, stale and
revoked history, explicit approval without duplication, single-study
generalization rejection, outcome exposure, and supplied-only versus actually
executed use.

## Inspect the synthetic two-inquiry path

The method acceptance fixture captures a candidate in inquiry one, records the
policy-owned validation and activation decision, then starts a compatible
inquiry two through the normal 003 orientation path. It asserts that the
second inquiry receives and uses the exact active method version without a
save/recreate prompt. Inspect the committed skill version and use records
beside the second inquiry's answer/trace. The trace includes exact supplied
version/reference IDs; executed example IDs appear only if that fixture
actually runs the recipe. A single-study Result proposed as a general skill
must stay a candidate with provenance/exposure and reach zero unrelated
inquiries as an authoritative unscoped rule.

Before 004 runs, the source acceptance fixture passes an exact committed 014
candidate through the real 022 provider, independently replays its bounded
recipe under the intended grant, preserves assertion provenance and
unresolved definitions, records existing configured authority/conflict
checks, and commits one immutable active version through 009. Provider
orientation sees that version through 015's compatibility predicate. Feature
004 later consumes the committed version in its live source-inquiry
demonstration.

Owner-review and withdrawal command fixtures exercise the registered
`skills review` and `skills revoke` operations using the configured synthetic
owner principal. A worker or non-owner principal receives an access denial and
creates no authority decision.
