# Quickstart: Recurrence and Consequential Use

This guide validates the 005 flow on a synthetic local source. It does not use production records or send messages externally. The implementation path expects the graph prerequisites 004, 016, and 017; 011 is available transitively through 004 → 003 → 002.

## Run the deterministic acceptance fixture

From the repository root:

```powershell
pnpm build
pnpm exec tsx --test src/analytics/acceptance/005.test.ts
```

Expected assertions include two distinct bounded source windows, a same-schema definition change classified `incomparable` without an unqualified trend, a missing-metadata pair left `unassessed`, a recorded repeated look, a report and Decision that predate the late correction, all nine correction-impact record categories, the stale prior assessment and a new current assessment. The assertions also keep Claim evidence separate from authorization and show reproduction independently from execution success and availability.

## Open the local synthetic demonstration

Run the fixture adapter through the ordinary production path:

```powershell
pnpm exec tsx src/analytics/demo-recurrence.ts --fixture recurrence-use-synthetic --model gpt-6-luna --reasoning xhigh
```

The demo should print the inquiry and committed-record IDs and provide the existing answer/trace viewer route. In the viewer, follow the report assertion to its Claim, Result, Checks/Review, Plan/look history, source window and correction, source-skill version, Use Assessment and Decision. The correction panel also links the synthetic answer-card and ledger references supplied to 016's impact traversal.

Expected evidence remains scoped:

- The earlier Result, Claim, report and Decision remain inspectable with their old revisions.
- The correction creates a new source version and linked impact. Current use becomes `stale` before the new assessment is written.
- The new assessment names exact current evidence and policy revisions. Risk acceptance, if configured, records the accepted uncertainty separately.
- The viewer displays repeated-look exposure and does not call an undeclared look an independent confirmation.
- Saving an internal report does not create publication authorization. An empty review rationale remains `pending_review`.
- Inconclusive and conflicting routine findings remain honest internal answers; they are not relabeled `supported`.

## Run the feature and viewer checks

```powershell
pnpm test
pnpm smoke:viewer
```

Use the recorded output and viewer assertions to fill the evidence rows in [verification.md](verification.md). See [the integration contract](contracts/recurrence-use-integration.md) for the owner boundary and scenario protocol, and [the data model](data-model.md) for records and relationships.
