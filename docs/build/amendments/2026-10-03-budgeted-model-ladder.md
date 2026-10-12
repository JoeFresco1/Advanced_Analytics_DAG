# Superseding build model and retry policy

User correction supersedes the earlier autonomous escalation amendment. This changes build workers and independent model reviewers only, not analytical execution or pinned analytical acceptance models.

- Luna xhigh: one attempt plus two retries.
- Sol 6.1 medium: one attempt plus two retries.
- Sol 6.1 xhigh: one attempt plus three retries.
- No Terra and no automatic Astra. Exhaustion requires intervention.
- Reviewer runs separately at the same tier as the worker. Deterministic checks remain required.
- Only rejected work consumes this semantic repair budget; infrastructure recovery remains separately bounded. Event history survives manual retry-cycle resets.
- Existing task_81cde6a2 had two Luna failures and one premature Astra failure. All three remain evidence. Attempt 3 does not consume this replacement ladder's tier budget. One final Luna attempt remains, recovering latest salvage attempt 3 (3a75334c6478691f66cff23107778947d799e6a4).
- Codex CLI 0.156.1 currently rejects gpt-6.1-sol for this account. Runtime policy explicitly holds that tier unavailable until verified supported or the user authorizes a specific substitute. It cannot bypass to a more expensive model.

Verification: node --test scripts/build/escalation.test.mjs scripts/build/recovery.test.mjs: 8 tests passed, including tier boundaries, deduplicated failures, no automatic Astra, migration, unavailable model guard, crash recovery, and Windows launcher.
