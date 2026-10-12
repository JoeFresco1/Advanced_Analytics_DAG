# Quickstart: Verify the Inferential Profile

This guide is for the implementation phase. It uses the local synthetic
renewal fixture and the existing CLI/browser route; no external data is
needed. The 006 acceptance test must keep evaluator answers outside the live
worker packet.

## Prerequisites

- Graph prerequisites 001, 003, 007, 010, 016, and the synthetic evaluator
  fixture bootstrap are present.
- The authenticated local principal has the fixture grant.
- Run budget and frozen inferential method/check policy are available.

## Deterministic acceptance

```powershell
pnpm test
uv run --project python pytest
pnpm smoke:viewer
```

Expected: known-effect, null, and low-power cases retain the signed risk
difference and declared interval; invalid assumptions name their limitation;
no outcome-driven case is relabeled confirmatory; and the browser answer
links to the exact Plan, denominator, Result, checks, execution, and source
version. See [verification.md](verification.md) for the evidence register.

## Synthetic live-question path

Use the documented fixture ID from the 18-bootstrap registry in place of
`<renewal-fixture>`:

```powershell
dag analytics ask --project <local-project> --question "How different are renewal rates between the two defined customer groups during the fixed renewal period?" --source <renewal-fixture> --model gpt-6-luna --reasoning xhigh
dag analytics answer show --inquiry <inquiry-id> --open
dag analytics trace --claim <claim-id>
```

Expected answer fields: population and period, group definitions, distinct
customer denominators, estimate and signed direction in percentage points,
interval method/level, dependence assumptions, practical margin, comparison
family, interpretation/exposure, limitations, and trace links. If data or
design do not support the named population, the answer narrows scope or
states the limitation. It never presents the observational difference as a
causal intervention effect.

## Fault scenarios

The acceptance test seeds the source in turn with a too-small group, repeated
customer observations without a valid reduction, unsupported cluster
dependence, missing/changing group membership, a nonrepresentative sample,
name-derived sensitive labels, an outcome-selected subgroup, undeclared
repeated looks, and causal-overreach wording. Each case must preserve its
reason and exposure evidence and return a canonical scoped outcome. A
predeclared sequential fixture follows the locked 016 rule and records every
look.
