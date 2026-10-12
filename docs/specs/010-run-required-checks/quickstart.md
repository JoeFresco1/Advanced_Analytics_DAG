# Quickstart: Required Check Acceptance

These are the implementation-stage validation commands. They use only the
synthetic/local fixtures defined by feature 010 and do not require source data
or network services.

## Prerequisites

- Node.js 20 or newer and the repository's installed pnpm dependencies.
- The 009 stage and evidence publication interfaces available from the
  feature dependency.
- No edits to `dag.run.json`, shared interface files, policy thresholds, or
  fixture reference answers.

## Run the acceptance suite

```powershell
pnpm build
pnpm test
```

Expected result: TypeScript builds, the Node test suite passes, and
`src/analytics/acceptance/010.test.ts` demonstrates the frozen policy and
Check Result contract. The feature verification report records the actual
command exit, fixture counts, FR/SC evidence links, and limits; planning does
not pre-claim these commands have passed.

## Required fixture observations

1. A synthetic split with twelve overlapping entities produces an execution
   record showing the validator ran and a bound Check Result with
   `outcome: fail`; the dependent Claim has
   `supported_presentation_eligible: false`, and no favorable-result retry
   is admitted (T03).
2. Missing, renamed, or worker-replaced validators fail frozen-policy
   coverage; an old subject digest or policy revision is stale (T02/T10/T23).
3. A valid negative baseline comparison is retained without another attempt
   intended to produce a favorable metric (T04).
4. Crashed and malformed checkers are recorded as `error`; missing/stale
   coverage stays distinct from Check Result outcomes.
5. Current `pass` and a policy-justified `not_applicable` satisfy their
   required conditions; all other states in the contract table block. The
   N/A record remains visibly distinct from pass.
6. An owner-registered version must pass schema and policy fixtures before
   its first dependent execution. Worker-authored permissive checks and
   unregistered profiles cannot make a Claim eligible.

The acceptance test proves only its named validator failure classes. The
viewer trace is consumed by feature 011 and the fixed-analysis integration
path by feature 002.
