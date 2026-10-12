# Missing upstream repair owner — 7 October 2026

Run: `run_b3a47590`; failed unit: `002-scope-t005-t006` / `task_1f08e703`.
Evidence: controller incident `49fda4d1388b889e691e915a538992a0b9c5d66b6f92c8e03c71830809f4aefd`, attempt logs and acceptance output in the project's `.dag-build/controller-recovery/run_b3a47590/`.

The diagnosis correctly identified that the 011 viewer rendered program and environment pins as "available" without their exact committed digest. The coordinator scope resolver required an exact source filename in diagnostic prose. This diagnosis named the feature, so recovery retained only the consumer's test/demo files. Repeated repairs could not change the responsible viewer and exhausted two hours. Existing 002 browser cleanup also retained an inherited stderr pipe, causing some verification attempts to time out.

The repair renders valid available pin digests as text, preserves unavailable/redacted behavior, and releases the browser helper's owned stderr pipe after teardown. Recovered 002 implementation and its added integrity controls were retained. Existing test assertions and requirements were not weakened.

Recovery now resolves feature references using registered upstream owners and accepts explicit, narrow missing-owner requests through a trusted controller hook. Scope expansion never changes acceptance and stays within the same budgets. Future/unregistered features and frozen files are rejected. Repair workers use focused tests while the controller performs full acceptance.

Verification: build passed; 599 Node tests passed; 24 Python tests passed; all structural coverage references resolved across 23 specs, 234 FRs and 114 edge cases; 30 shared and 16 project diagnostic/recovery tests passed. Real 002 browser checks verify exact pins, altered/missing evidence rejection and recovery. An initial simultaneous scoped browser launch exited before CDP readiness; a subsequent scoped run passed both checks. This startup flake is recorded, not claimed fixed by the ownership change.

A structural audit inspected all 104 unfinished implementation units and each registered upstream feature repair scope: no unresolved dependency references or oversized scopes were found. This is an ownership audit, not semantic certification of unbuilt features. The unchanged retry/model budgets and all historical failures remain recorded. Normal task checks and separate review must still determine completion after publication.

Independent Luna xhigh review accepted the product repair and the final owner filter. Review counterexamples led to excluding upstream acceptance directories and test/spec filename patterns across every permitted source extension. The final verdict is retained in C:/dag-tmp/aa-owner-recovery-review-4.md. Completion still belongs to normal DAG task review.
