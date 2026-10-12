# Research: Durable Analysis-Attempt Ledger and Repeat Detection

This feature's engineering choices are resolved by the accepted feature specification and frozen shared interfaces. No external research or unresolved technical choice is required.

## Decision 1: Project memory is the authority

**Decision:** Store immutable attempt events and linked domain references through 009 commitProjectMemory/recoverProjectMemory. Rebuild the ledger's current projection and 019 search index from committed manifests and rationale events.

**Rationale:** The shared contract makes project memory append-only and CAS-protected, defines stable commit replay, and explicitly assigns 009 persistence and 019 indexing. A second file store, SQLite authority, or catalog transaction would violate ownership and weaken recovery.

**Alternatives considered:** A mutable per-inquiry JSON ledger, a ledger-owned database, and treating the FTS index as history. Each duplicates the authoritative memory stream or loses rebuildability.

## Decision 2: Reuse the 012 identity predicate

**Decision:** Import compareAnalyticalIdentityV1 and add only 023's attributed question-semantic judgment and canonical match classification.

**Rationale:** 012 owns AttemptIdentityV1 and its single comparison predicate. The frozen dimensions include exact source versions and roles, population, denominator, target, transformations, method/profile, code, parameters, and evaluation contract. 023 must not fork this comparison.

**Alternatives considered:** Implementing a second exact-match helper or using question text/name similarity. Both could disagree with admission and miss structured conflicts.

## Decision 3: Use the 003 provider seam

**Decision:** Export analyticsExtensionV1 from ledger.ts with the ledger command and the orientation, admitted, execution, and finalization hooks. Do not edit shared controller or CLI files.

**Rationale:** 003 owns dispatch and fixes hook order. The provider table assigns 023 ledger show, orientation, admitted/execution events, and fallback finalization to this module.

**Alternatives considered:** Registering global callbacks from ledger.ts, directly editing controller/CLI, or adding a second ledger runner. These bypass the extension contract or duplicate shared infrastructure.

## Decision 4: Separate attempts from executions and intentional replication

**Decision:** One attempt represents one admission of a comparison purpose under one Plan revision. A retry or candidate execution appends an execution event within it. Authorized replication creates a distinct linked attempt with a distinct purpose instance and attributed rationale, while preserving the same Plan if unchanged.

**Rationale:** FR-001 and FR-012 make admission the attempt boundary and explicitly distinguish process retries from replication. This preserves denominator and history semantics.

**Alternatives considered:** One ledger row per process invocation, or treating same-identity replication as an infrastructure retry. Both erase the analytical admission boundary.

## Decision 5: Preserve scope, access, and uncertainty in orientation

**Decision:** Filter to current accessible records before returning matches, return assessment references rather than prior outcomes, and record ledger retrieval/exposure refs. 020 remains the answer-card ranking and actual worker-outcome exposure owner. Reassess source availability and grant eligibility before any reuse.

**Rationale:** FR-005 and shared interfaces prohibit 023 from ranking cards or supplying prior outcomes to the worker. Access and judgment rules require attribution, access-before-ranking, and safe historical references.

**Alternatives considered:** Returning full historical answers from orientation or treating a redacted/revoked source as reusable because the old attempt was committed. Both violate the trust boundary.
