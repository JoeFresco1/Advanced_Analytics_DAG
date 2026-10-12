# Coordinator recovery: execution output boundary and verification liveness

Date: 2026-10-06. Run: run_b3a47590.

The predictive calculator's prior root guard compared staged outputRoot with process cwd. Production stages deliberately separate the input working directory from their execution output root. The controller now supplies ANALYTICS_EXECUTION_ROOT from its validated execution root through the neutral task environment. Staged outputRoot must resolve beneath that independent root; absolute path, symlink-parent, and declared-relative-path containment checks remain mandatory. Missing controller authority fails closed. This is workflow enforcement, not an operating-system sandbox guarantee.

The recovered calculator retains numerical, partition, maturity, uncertainty, exact-binding and no-post-result-search checks. It passes 13 scoped tests, including a separate input/output directory control and forged root rejection. The production stage passes 8 focused checks, including overriding an inherited forged environment value.

The recovered viewer completed all assertions but its test process remained alive. On Windows, Edge helper processes can retain the spawned browser's stderr pipe after its primary process exits. Browser fixture teardown now explicitly destroys that owned pipe. All 29 viewer checks pass and exit normally in 24 seconds; there is no forced successful process exit.

Build verification commands now stream their actual progress and have a 15-minute per-command watchdog. The Windows watchdog terminates the command's process tree and reports failure; it cannot approve timed-out work. Two guard tests prove nonzero rejection, actual output capture, and termination of a silent stalled child.

The two DAG units still require their normal independent reviewers and task completion records. Old attempts and diagnostics remain in history. Only the three predictive semantic rejections caused by the corrected root contract are excluded from the new Luna budget. A recovery timestamp prevents stale checkpoints from overwriting the coordinator's repaired baseline. Model policy remains Luna xhigh, then Sol 6.1 medium, then Sol 6.1 xhigh; automatic Astra escalation is disabled.

Final coordinator validation: TypeScript build passed; full Node suite 568 passed, zero failed/skipped; Python suite 24 passed; spec coverage audit resolved all 234 FRs and 114 edge cases across 23 specs.
