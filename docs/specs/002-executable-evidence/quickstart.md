# Quickstart: Execute and Inspect Evidence

All commands use the repository root and synthetic/local fixture data. Run them after the 009, 010, 011, and 18-bootstrap prerequisites plus the 002 implementation are present.

## Build and acceptance

    pnpm build
    node --test dist/analytics/acceptance/002.test.js
    pnpm test
    uv run --project python pytest -q
    python scripts/audit_spec_coverage.py

The focused stage-call command verifies that the synthetic computation goes through 009 `executeStageV1`. The full `pnpm test` run includes the 002 real-browser acceptance: `src/analytics/acceptance/002.test.ts` launches installed Edge or Chrome with an isolated profile and drives the production 011 route over CDP. Set `DAG_ANALYTICS_BROWSER_PATH` to select a nonstandard browser location. Browser absence or startup failure fails the acceptance; it is never treated as a skip. No Playwright/Puppeteer dependency is required.

## Run the fixed demo

    node dist/analytics/demo-evidence.js --scenario matrix

Expected: the command prints one operation summary per fixture scenario, exact committed record IDs and manifest revisions for publishable outputs, and explicit evidence/check/eligibility results for failures. The null-improvement case is retained with no method retry. Missing-output and malformed-check cases are not promoted. Concurrent publishers report one committed revision and one revision_conflict. Recovery/replay shows one logical result. The browser acceptance in `pnpm test` opens the actual 011 route and records the browser/version, route, and visible page trace; it does not count a skipped browser as passing.

## Inspect in a browser

For an interactive inspection, run node dist/analytics/demo-evidence.js --scenario pass --serve and open its printed local browser URL in Edge or Chrome. Select the displayed result value and follow its trace through the claim, check/policy outcome, execution, Plan and program/environment pins, transformation/source version, and committed artifact. Confirm the pass trace uses current evidence; overlap/malformed/missing/changed-artifact variants state the precise blocking reason; denied/redacted variants return no raw protected bytes. For automated acceptance, the 002 test-local CDP helper performs the same route interaction and visible trace assertions. Record the real demo/test commands, exit codes, browser/version, route, action/observation, committed IDs/revisions, and captured browser evidence in verification.md after acceptance. A planned route or unexecuted command is not evidence.

Do not edit dag.run.json. Each crash/race/access fixture operates on its own synthetic sidecar and never touches a user's run, source, or project files.
