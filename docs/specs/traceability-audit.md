# Invariant-to-requirement audit

**Scope:** The 23 Spec Kit specs and proposal revision 2.6. A proposal trace
names only invariants for which the spec has a numbered functional
requirement. The FR mapping below is a specification cross-reference, not a
claim that software has passed an acceptance test.

| Spec | Claimed invariant → owning FR |
| --- | --- |
| 001 | I04→FR-005; I13→FR-008; I14→FR-009 |
| 002 | I01→FR-001; I02→FR-006; I03→FR-002/004; I04→FR-003/005; I06→FR-004; I08→FR-008/010; I12→FR-007; I14→FR-001/007/009 |
| 003 | I01→FR-004; I02→FR-011; I04→FR-014; I05→FR-003; I07→FR-005; I08→FR-012/013; I10→FR-009; I13→FR-012; I14→FR-004/010/011 |
| 004 | I08→FR-003; I11→FR-001/007/008; I14→FR-002/007/008 |
| 005 | I05→FR-001/009; I08→FR-011; I09→FR-005/010 |
| 006 | I02→FR-005/009; I05→FR-002; I07→FR-001/003; I08→FR-003/010; I14→FR-009 |
| 007 | I07→FR-002/004/007; I08→FR-005/007 |
| 008 | I05→FR-004/005/006/008; I08→FR-003/007 |
| 009 | I01→FR-001; I12→FR-003/004 |
| 010 | I03→FR-001/004; I04→FR-002/005; I06→FR-003/007 |
| 011 | I02→FR-002/003; I08→FR-003; I14→FR-001/003 |
| 012 | I04→FR-004; I08→FR-005; I10→FR-001/003/008; I13→FR-005 |
| 013 | I04→FR-005; I07→FR-002; I13→FR-007 |
| 014 | I08→FR-007; I11→FR-001/004/007 |
| 015 | I05→FR-004/005; I08→FR-004; I11→FR-001/002/003 |
| 016 | I07→FR-002/003; I08→FR-005 |
| 017 | I05→FR-004; I09→FR-001/005/007 |
| 018 | I14→FR-001/012; I15→FR-007; I16→FR-007; I17→FR-008; I19→FR-009; I20→FR-010 |
| 019 | I15→FR-001/004; I18→FR-008/017; I19→FR-009/012 |
| 020 | I17→FR-005/006/010/014; I18→FR-009/016; I20→FR-013 |
| 021 | I16→FR-001/002/003; I17→FR-004 |
| 022 | I11→FR-005/007; I16→FR-001/002/003; I17→FR-004/006 |
| 023 | I04→FR-004/007; I08→FR-003/006; I17→FR-009; I20→FR-001/005/007 |

The overclaims removed in this pass include I13 from 002/004/005/006/014,
I12 from 005/016, and I03/I06/I09 from 003/006. System-wide invariants still
apply to every implementation; this table states which behavior a particular
spec claims to own or verify.

## Previously unowned proposal cases

- T53 is the worker-context and neutral-runner acceptance case for 001.
- T54–T55 and T58 cover the first inferential profile and repeated looks in
  006/016.
- T56–T57 test retrieval ranking and no-match behavior in 020.
- T59 tests the owner-approval path in 022.
- T60 tests unsafe report content in 011.
- T61 tests restart continuity after evaluation exposure in 003/008.

The [FR verification map](fr-verification-map.md) names one primary numbered
SC or proposal T-case for each of the 234 numbered requirements. The
[edge verification map](edge-verification-map.md) assigns each of the 114
listed edge cases to an owning FR and a numbered acceptance check. Run
`python scripts/audit_spec_coverage.py` at the repository root to check that
all 23 spec sections, IDs, case texts, and referenced checks still resolve.
The maps describe acceptance obligations. No implementation fixture has been
run merely because a checklist box is checked.

## T-case ownership audit

The table names the primary focused-spec owner for each proposal case.
Milestone specs may cite a case to test its integrated path; that does not
make them owners of the focused rule. Feature 018 runs a frozen evaluation
over contributed cases and owns the evaluator, not every domain contract.

| Primary owner | Proposal cases |
| --- | --- |
| 001 | T53 |
| 007 | T05 |
| 008 | T06, T07, T09, T61 |
| 009 | T01, T02, T13, T14, T15, T22, T52 |
| 010 | T03, T10, T23 |
| 011 | T11, T60 |
| 012 | T12, T26, T27 |
| 013 | T04, T08, T24 |
| 014 | T17 |
| 015 | T18, T19, T25 |
| 016 | T20, T58 |
| 017 | T16, T21 |
| 019 | T28, T29, T30, T37, T43, T44, T45, T46, T47 |
| 020 | T31, T35, T36, T39, T40, T41, T42, T56, T57 |
| 021 | T32, T38 |
| 022 | T33, T34, T59 |
| 023 | T48, T49, T50, T51 |
| 006 | T54, T55 |

The previous broad traces were narrowed where they lacked direct coverage:
003 no longer claims concurrency/reproduction cases T14–T15; 006 no longer
claims T21 or I04; 007 no longer claims T08/T20; 009 no longer claims the
policy-change case T23; 020 no longer claims ledger cases T48–T50; and 022
no longer claims capture/pinning cases T32/T38/T40. Shared cases cited by
other focused specs are supporting behavior, not duplicate primary ownership.
