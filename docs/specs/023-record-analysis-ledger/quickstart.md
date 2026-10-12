# Quickstart: Verify the Analysis-Attempt Ledger

All scenarios use the feature's synthetic/local fixtures. Do not use remote or confidential source data. This guide runs after dependencies 003, 009, 012, and 019 are implemented.

## Build and acceptance

From the repository root:

    pnpm build
    pnpm test

The 023 acceptance cases live in src/analytics/acceptance/023.test.ts and are included by the project test script. A successful process exit is only a software check; the fixture assertions separately check recorded evidence and canonical outcomes.

## User-visible scenarios

1. Run the synthetic inquiry fixture whose worker crashes after a committed execution and writes no prose. Open its inquiry timeline. The row must contain only committed question, method, data version, execution, result-or-absence, evidence, and rationale facts, with interpretation explicitly missing.
2. Open the fixture with a null result and failed check. Both remain searchable work history and neither is a supported answer.
3. Ask the fixture orientation provider to assess the same scoped question and exact identity. The prior attempt and all equal dimensions appear before any new execution. A reuse or deliberate replication rationale is recorded before proceeding.
4. Orient a newer period or cohort. The match is related, its changed scope and comparison link are visible, and the new analysis may proceed.
5. Rebuild project memory/index after fixture commit, then repeat the safe ledger show. Stable attempt/event IDs, rationale, refs, and access scope remain unchanged.

The user command contract is dag analytics ledger show --inquiry <id>. The fixture supplies its synthetic inquiry ID; current grants apply to each read.
