# Diagnostic recovery of the stopped analytics build

Two fresh Luna xhigh diagnostics classified the existing failures as
contract/ownership blockers, not demonstrated need for a stronger model.
Original diagnostic responses and run history remain under
`.dag-build/diagnostics/run_b3a47590/`.

## Reproduced causes and coordinator remedies

008 T011 explicitly requires writing its verification report, but the frozen
contract lock included that report. Six attempts stopped at the hash guard
before acceptance. Remove generated verification reports from the lock only
when an existing implementation unit explicitly owns that output. Keep specs,
plans, execution manifests, quality checklists, thresholds and tooling frozen.
Current evidence is still subject to the existing report and acceptance gates.
The recovery audit retains each report's former hash; this does not approve any
new report content.

011 T003 requires exercising the real viewer. The upstream HTTP facade sends
`script-src 'self'`, which blocks the generic viewer's inline client. The browser
unit owns tests and HTML, not http.ts. A coordinator repair to http.ts authorizes
only the explicitly identified server-owned client by its SHA-256 source hash,
normalizing HTML line endings. Arbitrary injected inline scripts remain blocked;
no unsafe-inline or unsafe-eval permission is introduced. Existing real browser
checks include the injected-script negative control and CRLF case.

## Recovery accounting

Preserve all rejected attempt IDs and their evidence. The prior 011 attempts
and six rejected 008 attempts arose under these broken controller prerequisites;
exclude them from the corrected task's implementation model budget explicitly,
rather than hand-resetting task state or declaring success. The corrected
attempts begin with Luna xhigh and retain the established bounded ladder.
Advance the recovery floor to prevent an earlier checkpoint from undoing the
coordinator's fix. Do not touch completed tasks. Requeue failed tasks using the
public CLI, retain all acceptance/review gates, and verify actual worker launch.

Coordinator verification before launch: recovered 008 acceptance runs 57 tests
with zero failures; 011 initially reproduces CSP and browser load failures.
After the HTTP repair, require the same focused browser checks, including a
working client and blocked injected script, before restarting the graph.
