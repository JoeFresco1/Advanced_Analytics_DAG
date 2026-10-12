# Access and semantic judgment contract

**Status:** Normative cross-cutting contract. Feature 001 owns principal and
controller enforcement; feature 010 owns frozen analytical policy profiles;
features 008, 012, 017, and 020 own their named domain judgments. This
document specifies their common inputs and trust boundaries.

## Access decisions

Every request has a principal ID from the local authenticated session or CLI
identity and zero or more project roles assigned by project configuration.
The worker cannot create its own principal, role, grant, or policy. A source
grant names the principal or role, source identity, allowed operation (metadata,
aggregate, row, sheet, export, or administration), audience, and effective
period. An absent identity or grant denies the operation. Source discovery
records the grant and its owner; a candidate skill cannot enlarge it.

The controller filters references and catalog candidates **before** they enter
worker context. Source adapters check grants before queries and workbook-sheet
reads. The viewer checks the current principal again when a trace or artifact
is opened; a prior worker grant is not a viewer grant. Search indexes store
only permitted summaries and carry source scope so rebuilding cannot broaden
visibility. Retrieval records what was exposed to the worker. Redaction and
revocation change current availability while preserving an access-safe
historical pointer.

This first local product enforces those rules at its controller, adapters,
catalog, and viewer. If a worker shell can read source bytes outside these
surfaces, that fact is displayed as a workflow boundary, not filesystem
isolation. Strong row or sheet secrecy against that worker requires a host or
source permission boundary outside its shell. No acceptance criterion claims
stronger isolation than the fixture actually supplies.

## Judgment provenance

Deterministic code verifies identities, digests, changed locked fields,
enumerated states, policy versions, numerical rules, budgets, and access
grants. The worker proposes question relevance, answer usefulness, candidate
skill value, and the semantic match of an answer card, with a reason and
evidence IDs. A named owner or reviewer decides disputed meanings or
authoritative definitions. The controller records the proposer, reviewer,
reasons, version, and outcome; it does not label a worker assertion as a
mechanical validation.

In feature 020, the query and card have explicit source, population, metric,
period, and question-intent fields. Exact known fields can be filtered and
ranked mechanically. Missing or ambiguous intent makes a card `related` at
most; the query outcome is `no_match` if none qualifies as a direct answer.
Worker-authored relevance explanations remain attributed
to the worker. The frozen retrieval fixture has independent relevance labels;
its Hit@3 score measures the ranking implementation against those labels,
not the controller's ability to prove meaning in arbitrary prose.

## Policy authorship

A project owner registers a versioned policy profile before a method uses it.
The profile declares method applicability, required validators, permitted
`not_applicable` reasons, claim-presentation rules, authority routing, and
effective scope. Registration validates the schema and runs policy fixtures.
The worker can propose parameters or a new profile; it cannot publish the
new profile, relax a required check, or appoint the approving owner. A policy
change is a new version and re-evaluates dependent current use assessments.
