# Fixed Evidence Demo Contract

**Status**: Feature-local integration contract; it consumes frozen 001/009/010/011 contracts and adds no domain vocabulary.

## Runnable command

After the feature is built, run the demo from the repository root with:

    node dist/analytics/demo-evidence.js --scenario matrix

The command uses a temporary local synthetic project/run unless a fixture root is explicitly supplied. Matrix mode runs every fixture selector and exits with a scenario summary and committed revisions. A single case may be selected with --scenario followed by one of pass, overlap-fail, null-improvement, malformed-check, checker-exit, unsupported-not-applicable, omitted-check, stale-check, missing-output, changed-artifact, concurrent-publication, pre-commit-recovery, acknowledgement-replay, inaccessible-evidence, redacted-evidence, or serialization-equivalent. For interactive inspection, pass mode also accepts --serve, keeps the local viewer route available until stopped, and prints its URL. The `src/analytics/acceptance/002.test.ts` CDP harness launches an installed browser with an isolated temporary profile, starts the local 011 HTTP route, navigates to it, and asserts the rendered trace. It uses Node's built-in `fetch` and `WebSocket`; no Playwright/Puppeteer package or shared smoke script is assumed. Resolve the executable from `DAG_ANALYTICS_BROWSER_PATH` when set, otherwise try Windows `C:/Program Files (x86)/Microsoft/Edge/Application/msedge.exe` and `C:/Program Files/Microsoft/Edge/Application/msedge.exe`, Linux `/usr/bin/microsoft-edge` and `/usr/bin/google-chrome`, then macOS `/Applications/Google Chrome.app/Contents/MacOS/Google Chrome`. Browser absence or startup failure is an acceptance failure, never a skipped check. The harness prints the browser/version, route, and observed page content for `verification.md`. These are fixture selectors, not analytics states.

The command is a direct feature-owned demo entry point. It does not add a dag analytics subcommand or edit the CLI owned by 003. The page and APIs it opens are the 011 production routes and viewer, backed by committed records. `pnpm test` runs `src/analytics/acceptance/002.test.ts`; that test owns the CDP launch/navigation/DOM assertions and fails if the installed browser or 011 route is unavailable.

## Frozen producer and controller boundary

1. Load the fixed synthetic source and a fixture PredictivePlanV1 member of PlanRegistrationV1 validated against the 001-generated schema. Preserve its committed Plan ref/revision/digest as immutable input for the complete demo.
2. Construct the standard computation request with exact Plan/source/transformation/program/environment/attempt bindings and declared output IDs/paths. Pass the request by reference and use executable/argv semantics.
3. Invoke python/analytics_worker/demo_evidence.py through 009 executeStageV1. The Python fixture computes only its deterministic synthetic case and validates the standard Pydantic request/result contract. It is not a reusable descriptive/predictive calculator and does not call a TypeScript calculator.
4. Publish complete result and output records with 009 commitEvidence. A staged output is not visible as committed evidence. Exercise recovery and expected-revision CAS with isolated run sidecars and stable commit IDs.
5. Run the frozen required checks through 010 on the exact committed artifact refs and digests. Record check execution completion separately from canonical check outcome. Missing, malformed, stale, failed, and unjustified not_applicable conditions block supported presentation while retaining available evidence. A valid null/no-gain result is retained without automatic method retry.
6. Bind the report selector and claim to the exact committed result, check, policy, Plan, transformation, and source refs. Open it through the 011 HTTP/viewer routes; the viewer resolves committed IDs and applies current access on the browser request.

No 002 implementation changes contracts.ts, store.ts, publication.ts, stages.ts, checks.ts, policies.ts, report.ts, http.ts, viewer/analytics.html, shared schemas, graph/architecture documents, package manifests, or other feature files. A focused feature's accepted facade remains the authority even if its local implementation uses helper functions not named in this integration document.

## Observable case matrix

| Scenario | Required user-visible/evidence outcome |
| --- | --- |
| pass | One current result opens through Plan, program/environment, source/transformation, exact output digest, current checks and scope. |
| overlap-fail | Check process completion is separate from fail; the exact overlapping-entity observation remains visible and dependent support is blocked. |
| null-improvement | The fixed comparison result is retained with its declared uncertainty/classification; no execution is admitted solely to seek a favorable value. |
| malformed-check / checker-exit | The check is error (not pass); diagnostic evidence and reason remain inspectable; dependent supported presentation is false. |
| unsupported-not-applicable | not_applicable remains distinct and cannot satisfy the required policy without the frozen reason. |
| omitted-check / stale-check | Required coverage is missing or stale; the affected claim cannot qualify and the old pass is not reused. |
| missing-output | Even if the producer exits zero, no declared output means an evidence error and no supported report value. |
| changed-artifact | Digest/subject mismatch makes the prior check insufficient; current report presents an unsupported/broken link. |
| concurrent-publication | Two different commits share an expected manifest revision; one is committed and the other receives revision_conflict, without overwriting either staged artifact. |
| pre-commit-recovery | Staged/uncommitted output is invisible after recovery; replay leaves exactly one committed logical result. |
| acknowledgement-replay | Recovery finds the committed revision; replay with the same commit ID/content returns it without a duplicate. |
| inaccessible-evidence | A browser principal without the required grant receives denied/access-safe metadata only, not raw evidence bytes or a protected path. |
| redacted-evidence | The historic evidence link remains, current availability is redacted/unavailable, and reproduction is not described as successful. |
| serialization-equivalent | Changed bytes with equal canonical values use the declared semantic comparison and do not become a numerical mismatch. |

All negative, failed, missing, stale, inaccessible, redacted, and incomplete states remain historically inspectable. Process exit zero never promotes an analytical result or check by itself.
