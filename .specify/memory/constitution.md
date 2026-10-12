<!--
Sync Impact Report
Version change: 1.4.0 → 1.4.1
Modified principles: Product Constraints clarified through canonical state, access, and semantic-judgment contracts
Added sections: none
Removed sections: none
Follow-up TODOs: implementation fixtures and release evaluations remain;
the FR and edge-case coverage maps now resolve structurally
-->
# Advanced Analytics DAG Constitution

## Core Principles

### I. A Question Must Reach an Honest Answer

The worker MUST perform the analytical work needed to answer a delegated
question within its declared allowance. It MUST preserve the user's wording,
show consequential operational assumptions, and return a supported answer,
qualified uncertainty, or a precise reason the question remains unresolved.
Task completion alone MUST NOT be reported as an answered question.

### II. Execution and Evidence Are Different Facts

Every reported numerical assertion MUST resolve to an exact result,
computation, transformation, and data version. A successful process exit MUST
NOT imply a passed analytical check or a supported claim. Failed checks, null
findings, and conflicting results MUST remain inspectable; analytical outcomes
MUST NOT trigger retries intended to obtain a favorable result.

### III. Analytical History Cannot Be Rewritten

Plans, exposure to outcomes, computations, results, claims, reviews, and use
assessments MUST retain their identities and revisions. An outcome-informed
change MUST record a new plan or execution and preserve prior exposure.
Approval MUST NOT relabel historical work as pre-specified. Unknown exposure
or unavailable evidence MUST remain unknown or unavailable, never pass.

### IV. Project Memory Is Scoped and Tested

Every committed output MUST be inventoried with its origin or an explicit
unknown-origin label. Completed inquiries MUST remain findable as scoped
answer cards, including negative and inconclusive outcomes. Every admitted
analysis attempt MUST have a readable ledger entry linked to exact evidence;
later work MUST consult prior attempts and record reasons for deliberate
reruns. Reusable source,
method, and business-rule skills MUST state their authority, access scope,
meaning, version, and validation provenance. Later inquiries MUST record
which skill references or prior outcomes were supplied and what exposure they
introduced. Changed meaning, role, or freshness MUST invalidate affected
guidance. Skills MUST NOT contain credentials, raw sensitive records, or
untagged study-specific outcomes. A lost search index MUST be rebuildable
from committed evidence and durable curation records.

### V. Autonomy Has Bounded and Accurate Guarantees

The controller MUST bound time and discretionary work, preserve capacity for
required checks and finalization, and report a stop reason. Published output
count, bytes, and retention MUST follow declared limits; scratch files MUST
NOT silently become user-facing exports. Claimed access, write-root,
cost, reproducibility, and holdout guarantees MUST match what the environment
actually enforces or observes. Routine inconclusive internal answers MAY
complete without a human gate; consequential use follows its configured
authorization policy.

### VI. Features Must Work End to End

A feature MUST have a worker or computation producer, controller consumer,
inspectable user outcome, and a seeded failure demonstration before it is
called complete. The analytics product MUST keep software-factory instructions
and domain semantics out of its worker context. The neutral execution engine
MUST have one authoritative maintenance path across products.

## Product Constraints

The [product proposal](../../docs/specs/advanced-analytics-dag-proposal.md)
defines invariants I01–I20 and acceptance cases T01–T61. Feature specs MUST
cite relevant obligations and state their limits. Method profiles define the
evidence a claim requires; intended use and actual evidence state define
authorization. A worker MUST NOT weaken a required policy after a failure.
The [canonical state vocabularies](../../docs/specs/state-vocabularies.md)
govern machine values across all feature and milestone specifications.
The [access and judgment contract](../../docs/specs/access-and-judgment-contract.md)
governs principal/grant inputs and attribution of worker, controller, and
owner decisions.

The initial product uses existing non-interactive agent harnesses and a
neutral task executor. It does not require a paid platform. A local worker
with broad filesystem access MUST NOT be described as independently isolated
from local evaluation data.

## Delivery and Review

Work through the feature specifications in `specs/README.md` in dependency
order. Each feature plan MUST name its production caller, user inspection path,
data and trust boundary, failure fixtures, and compatibility effects. Use
deterministic checks for mechanical contracts and held-out reference fixtures
for whole-question evaluations. Set release thresholds before evaluation;
do not tune on every case and then report those same cases as generalization.

Reviewers MUST assess the full question-to-answer behavior and the limits of
each check. A check's pass proves only its declared property. Plans that add
objects without a working consumer do not satisfy this constitution.

## Governance

This constitution governs the Spec Kit feature, plan, and task artifacts.
Changes require an explicit amendment describing the reason, affected
features, and migration or re-evaluation required. Version changes follow
semantic versioning: major for incompatible principle changes, minor for new
or expanded principles, and patch for clarification. The project owner
authorizes governance and release-policy changes. Every feature review MUST
check compliance with the current version and document any exception with
its scope and owner; an exception cannot change a historical analytical fact.

**Version**: 1.4.1 | **Ratified**: 2026-09-27 | **Last Amended**: 2026-09-27
