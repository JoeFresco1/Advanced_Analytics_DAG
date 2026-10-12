# Quickstart: Verify Artifact Catalog and Delivery

All examples run against deterministic synthetic/local fixtures. The fixtures create their own files and grants in a temporary managed root; they do not use a user's home directory, real source data, external services, or external notifications.

## Prerequisites

- Complete implementation graph prerequisites 001, 003 and 009.
- Install repository dependencies using its existing lockfile and use the supported Node runtime with SQLite FTS5 available.
- Use the current principal fixture with explicit project, metadata, sheet and export grants. A missing grant must deny access.
- Read [the catalog contract](contracts/catalog-v1.md), [the import/delivery contract](contracts/import-delivery-v1.md), and [the data model](data-model.md) for record bindings and limits.

## Build and run the feature fixture

```powershell
pnpm build
pnpm exec tsx --test src/analytics/acceptance/019.test.ts
```

Expected result: build succeeds; focused 019 tests pass and report the feature's synthetic corpus and failure scenarios. This guide does not treat a successful process as evidence for any analytical Claim.

## Scenario coverage

1. **Register and recover generated output**: commit a run manifest with several declared outputs and exact Result/Claim usage links; leave a staged output and inject a lost catalog update. Rebuild from committed manifests. Expected: each committed stable ID appears once with digest, role, producer, availability, scope and usage links; staged output is not promoted.
2. **Search the crowded legacy folder**: import 99 readable CSV/`.xlsx` files with duplicate bytes, changed/missing paths and a misleading `final` name. Search by permitted schema/file metadata. Expected: all readable originals retain separate IDs and paths; duplicates group; provenance remains unknown; no answer card or finding is invented. Retry with 101 files. Expected: content for file 101 is not read or indexed.
3. **Exercise import ceilings**: use synthetic byte-overflow and controlled-clock time-limit cases. Expected: import stops at the declared limit, reports what it observed, and creates no record for uninspected content. Verify originals remain untouched.
4. **Exercise workbook grants**: dispatch `analytics artifacts import` through the real node-003 registry using one synthetic workbook with permitted, metadata-only and denied sheets. Expected: permitted sheet profiles only follow grants; metadata-only returns sheet metadata without cells; denied metadata produces an access-safe pointer without title or sheet name; restricted cell values appear zero times in profile, FTS, and worker context. Retain `f019-t39-restricted-workbook-denial` as supporting 019 catalog/access-boundary coverage; 020 owns primary proposal T39 acceptance.
5. **Bound exports**: request 1,000 exports from temporary tables under the default policy, then request one authorized workbook from a committed Result. Expected: zero default exports are published, the count violation is visible, and the single authorized export resolves to the pinned Result and answer card, which remain accessible independently.
6. **Recover retention**: retain evidence linked from the fixture answer, then interrupt a managed scratch sweep and recover twice. Expected: only eligible managed scratch/cache is deleted; both recoveries leave the same inventory; no evidence, import, user or out-of-root file is removed.
7. **Replay notebooks**: publish an optional notebook from fresh state with pinned inputs, environment, ordered cells and committed Result IDs; inject a hidden-state dependency and failed rerun. Expected: the clean notebook is linked; invalid notebooks remain exploratory and support zero Claims.
8. **Exercise the provider caller path**: load `analyticsExtensionV1` through node-003's registry, dispatch each catalog/import/export/retention/notebook command with a synthetic authenticated context, and deliver `afterEvidenceCommit` through the controller lifecycle dispatcher. The import handler is implemented in `catalog.ts` and delegates to the bounded importer in `import.ts`; caller paths and grants come from the typed context. Inject a post-run-commit hook/index failure and replay with the same IDs. Expected: typed command results are access-safe; committed outputs register once; the failed lifecycle acknowledgement stays incomplete until recovered.
9. **Rebuild the index**: delete/corrupt only the temporary SQLite projection and invoke the provider rebuild command against versioned synthetic committed run/project history containing artifacts, answer cards, ledger attempts, rationale, and match assessments. Expected: stable IDs, access scopes, availability, current validity, usage/card/attempt/rationale/match refs and canonical source bytes/history are unchanged; permitted readers can retrieve rationale, denied readers receive none, and staged/orphan files remain absent.

## Final repository acceptance

After the focused feature suite, run:

```powershell
pnpm test
```

The final implementation unit records actual command exit codes and evidence refs for every FR-001–FR-017, SC-001–SC-017, and EC-001–EC-007 in `verification.md`. It names 019-owned T28, T29, T30, T37 and T43–T47 fixtures, and identifies `f019-t39-restricted-workbook-denial` as supplemental coverage for 020-owned primary T39. Acceptance thresholds and reference labels remain unchanged.
