# Quickstart: Publish and Recover Exact Evidence

All validation uses temporary directories, synthetic records, and local artifact bytes. No user source data, remote service, or live worker is required for these cases.

## Prerequisites

- Node.js 20 or newer and the repository's installed dependencies.
- Node 001's analytics adapter and neutral execution workspace package available through the declared feature dependency.
- Worktree rooted at the repository.

## Run the focused fixture suite

Run the full build and acceptance suite:

```powershell
pnpm test
```

Run just the run-scope store/commit/recovery tests while working on that unit:

```powershell
node --import tsx --test --test-name-pattern=run-scope src/analytics/acceptance/009.test.ts
```

Run the project-scope store/commit/recovery tests while working on project memory:

```powershell
node --import tsx --test --test-name-pattern=project-scope src/analytics/acceptance/009.test.ts
```

Run the stage-binding and comparison fixtures:

```powershell
node --import tsx --test --test-name-pattern=stage-and-comparison src/analytics/acceptance/009.test.ts
```

## Expected evidence

- T01: `f009-t01-zero-exit-missing-output` uses an exit-zero synthetic command with a missing declared output; `f009-altered-output-digest` changes declared bytes. Both return an evidence error and publish no Result.
- T02: a synthetic check result bound to an older artifact is rejected as a subject mismatch; the old pass is not reused.
- T13: a simulated interruption after immutable artifact write but before manifest advance recovers with no visible partial Result.
- T14: two distinct commits at the same expected revision yield exactly one committed revision and one conflict, with neither output overwriting the other.
- T52: after the manifest advances but acknowledgement is lost, recovery plus the same commit ID returns one logical Result.
- Project memory applies the same race, replay, collision and interruption rules and rejects a cross-run reference until its run manifest is committed.
- T15: value-preserving serialization is reproduced only under a declared semantic comparison; a numeric mismatch remains mismatched.
- T22: redaction changes current availability while preserving an access-safe historical link and does not claim current reproduction.

`specs/009-publish-evidence/contracts/publication-v1.md` gives the boundary contract; `data-model.md` describes record relationships and invariants. Final implementation evidence and the FR/SC-to-fixture table are recorded by the last task in `verification.md`.
