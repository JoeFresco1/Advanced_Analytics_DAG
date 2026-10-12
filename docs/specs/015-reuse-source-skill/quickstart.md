# Quickstart: Source-Skill Reuse Acceptance

This guide describes the deterministic synthetic fixture path for feature 015.
It does not require a live source, credentials, or external network access.

## Prerequisites

- Node.js 20 or newer and the repository's existing pnpm install.
- Feature dependencies 003, 009, 014 and the frozen 019 project-memory facade.
- Synthetic source/version/grant/skill records created in
  `src/analytics/acceptance/015.test.ts`.

## Run the feature scenarios

```powershell
pnpm exec tsx --test src/analytics/acceptance/015.test.ts
```

The fixture exercises a cold source discovery followed by a warm inquiry
through the ordinary 003 registered orientation caller. It receives one exact
active skill version, its compatible entrypoint and references, and an event
for the exact recipe actually executed; project-memory effects pass through
the 009 writer. It also
passes same-schema changed unit, key/grain, role, timezone, lag, and definition
facts through the same predicate; appends an unused column; changes grants;
refreshes a skill; and includes a supplied-but-not-executed recipe and a
revoked historical pin. The supplied-guidance checks cover all canonical
lifecycle states and table-driven candidate variants across compatibility,
role/audience, and freshness.

Expected observations:

- A current compatible skill is pinned by its exact record reference and
  reduces repeated discovery without skipping required checks.
- A `candidate` never enters worker instructions as active guidance, even
  when compatibility, role/audience, and freshness are favorable; an allowed
  lookup returns only an access-safe review reference. `active` requires the
  full predicate, `stale` is limited to a safe historical pointer, and
  `revoked` supplies no current guidance.
- Each required unknown/mismatch is reported by the same predicate and only
  dependent guidance is unavailable for current use.
- The unused column does not stale unrelated guidance.
- Profile work runs only at its declared cadence or drift trigger; a pending
  profile does not permit current use.
- Refresh creates a successor record and keeps prior skill versions, run
  pins, findings, and exposure records intact.
- A narrower grant or revoked lifecycle blocks protected/current references
  while retaining access-safe historical pointers.
- Supply does not imply recipe execution; prior study outcomes produce an
  exposure record when supplied.

## Final project checks

```powershell
pnpm build
pnpm test
```

The implementation unit records actual commands, outcomes, fixture bindings,
and evidence paths in `verification.md`. A skipped or TODO scenario is not a
passing feature acceptance result.
