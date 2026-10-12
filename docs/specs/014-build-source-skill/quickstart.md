# Quickstart: Discover and Inspect a Source Skill Candidate

## Prerequisites

- The feature's 003 extension registry, 009 stage/project-memory services, and 019 catalog provider are available.
- 014 requires no 003 or 015 compatibility service. The later 015 orientation hook owns compatibility when active refs exist.
- A synthetic local SQLite source is registered under an opaque source ID.
- The authenticated project principal has a current metadata grant and aggregate grant for the fixture audience.
- The fixture contains schema, grain, join, definition, and time-boundary facts plus a deliberately unknown or conflicting meaning. No remote or confidential source is used.

## Run discovery

From the analytics project, invoke the frozen CLI action with the registered fixture ID:

~~~powershell
dag analytics source discover --source <registered-synthetic-sqlite-source-id>
~~~

The command checks the grant before reading, performs bounded metadata and aggregate discovery, executes generated parameterized Python examples through the existing stage executor, and commits one immutable candidate. It returns a safe candidate reference, source/environment/role/audience scope, the exact tested source window, and any bound or unknown-meaning limitation. It does not print rows, credentials, or a local database path.

The ordinary ask path invokes the exported production `orient(context)` hook. It reads currently accessible active skill refs for the exact source identity, environment, and tenant from committed project memory through 014's access-filtered projection over the committed 009 project head. If zero active refs exist, the hook verifies current metadata and aggregate grants plus remaining discovery budget, then calls the same bounded discovery function as the command. It commits only a candidate and returns its safe reference and scope metadata; it does not add candidate SKILL.md content to active worker guidance. If one or more active refs exist, the hook returns safe refs only and does not rediscover or judge compatibility; the later 015 orientation hook applies the compatibility predicate. Stale or revoked refs do not count as active, but 014 does not infer revoked authority or bypass current access checks. The acceptance test invokes both branches of this production hook separately from the command, proves no compatibility service is called by 014, and verifies grant-denied and exhausted-budget stops.

Inspect the returned committed candidate reference through the command result and the acceptance test's committed-package reader. The package includes the entry guide, scoped references, examples, provenance, and access metadata. Its lifecycle remains candidate; this command does not activate reusable instructions.

## Verify the full fixture path

~~~powershell
pnpm test
~~~

The Task 014 acceptance test creates a synthetic SQLite database in a temporary local directory and verifies:

1. Discovery finds the fixture's schema, bounded profiles, source assertions, unknown/conflicting definitions, grain/join observations, and time boundary.
2. A generated parameterized Python example executes successfully with the intended aggregate grant on the named fixture version/window.
3. The SQLite connection rejects writes; profiling does not publish raw source rows.
4. The candidate package is immutable, audience-scoped, and contains required provenance without credentials, sensitive row sentinels, or study outcomes.
5. Injected source text cannot change policy, access, or worker instructions; a narrow role cannot retrieve privileged metadata.
6. An unbounded recipe or one requiring a stronger grant remains candidate and does not run under the weaker audience.

The tests make no enforced filesystem-isolation claim for a local shell that can read beyond the product adapter. A missing immutable source version or snapshot guarantee is reported as unknown, not inferred from a query timestamp.
