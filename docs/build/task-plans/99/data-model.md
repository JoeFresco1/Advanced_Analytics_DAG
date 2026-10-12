# Final convergence evidence model

This model describes Node 99's acceptance and handover evidence only. It adds
no analytics-domain state, changes no feature record, and owns no persistent
product data. Feature records and their canonical states remain defined by the
accepted proposal, state vocabularies, feature specs, and frozen interfaces.

## Records

### `FeatureVerificationInventoryRow`

One row for each accepted feature key 001–023.

- `featureKey` and `reportPath` identify the feature and its
  `specs/<feature>/verification.md` file.
- `reportState` records present, missing, or structurally incomplete status
  for the report inventory. This is handover reporting, not an inquiry or
  Claim state.
- `frIds`, `scIds`, `edgeIds`, and `primaryTCaseIds` are derived from the
  feature spec and accepted maps/owner register.
- `actualEvidenceRefs` identify observed command/test output, evaluator
  records, browser evidence, or explicit missing-evidence observations.
- A planned/pending table does not count as actual evidence.

### `AcceptanceEvidenceRow`

One keyed row in `docs/build/COMPLETION.md` for each mapped acceptance item.

- `itemKind` is `FR`, `SC`, `EC`, `T`, or `PATH`.
- `itemId` is the stable ID from its frozen source. FR, SC, and EC keys are
  scoped by feature; T and PATH keys are global.
- `featureKey` names the owning feature for FR/SC/EC/T items, or the accepted
  milestone for a PATH item.
- `primaryCheck` preserves the FR or edge map link where one exists;
  `primaryOwner` preserves the exact T-case owner.
- `outcome` and `evidenceRef` report what was actually observed. The handover
  outcome label never rewrites analytics state or turns an analytical
  `inconclusive`, `unsupported`, `fail`, or `unavailable` result into a pass.
- `sourceReport` points to the feature report or accepted path evidence.

### `ObservedCommandEvidence`

An immutable observation referenced by acceptance rows.

- Captures exact program/arguments, working directory, runtime or harness
  version when available, start/end or batch ID, exit/result, stdout/stderr or
  report link, and produced record/artifact IDs.
- For `pnpm build`, tests, pytest, and the structural audit, preserves actual
  exit status and useful output.
- For browser inspection, identifies the observed route and captured screen
  or artifact reference. If no durable screen artifact is available, state
  that limit rather than inventing a link.

### `IntegratedPathEvidence`

One evidence record per accepted milestone path: 001, 002, 003, 020, 004,
022, 005, and 006.

- Names the producer, controller/consumer, inspectable CLI or browser outcome,
  synthetic fixture, injected failure, and actual inquiry/result/check/artifact
  IDs.
- Records separate success of execution from analytical result and retained
  failure evidence.
- Does not replace the milestone owner's `verification.md` or focused
  acceptance suite.

### `EvaluatorBatchEvidence`

Evidence for one feature-018 run.

- Pins evaluator/batch version, fixture lock and policy/threshold refs,
  harness/model settings, denominator counts, measured metrics, and actual
  outcome.
- Records cost as observed telemetry or unavailable; absent telemetry is not
  zero and is not a release gate.
- Records workflow-withheld/access-isolation limits alongside any held-out
  description.

## Expected completeness

The evidence matrix has exactly 637 keyed rows: 234 FR + 219 SC + 115 EC +
61 T-case ownership rows + 8 integration paths. A separate inventory has 23
feature-report rows. The test derives IDs from source specs/maps/registers and
requires exact equality with the matrix; repeated local IDs are disambiguated
by `featureKey`. T-case owners form the accepted one-to-one partition of
T01–T61.

Each matrix row has one outcome and a nonempty actual evidence reference. A
pass must point to observed acceptance evidence. A failure, blocked, not-run,
unavailable, or missing-report outcome must point to evidence of that exact
condition and remains a non-pass. Placeholder text, planned test descriptions,
unresolved links, missing denominators, duplicate rows, and unowned cases
fail the completeness check.

## Relationships

```text
FeatureVerificationInventoryRow ──owns── AcceptanceEvidenceRow
AcceptanceEvidenceRow ──cites── ObservedCommandEvidence
AcceptanceEvidenceRow ──summarizes── IntegratedPathEvidence
AcceptanceEvidenceRow ──summarizes── EvaluatorBatchEvidence
```

All source specifications, checklists, maps, thresholds, evaluator fixtures,
and verification reports are read-only inputs to Node 99. `99.test.ts`,
`docs/build/COMPLETION.md`, and `docs/QUICKSTART.md` are the only graph-owned
outputs.
