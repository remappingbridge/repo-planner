# UIC-00 candidate evidence

Status: **IMPLEMENTED CANDIDATE — HUMAN ARCHITECTURE ACCEPTANCE PENDING**.

Date: 2026-09-22.

## Gate

`UIC-00 — Semantic Inventory`

Planner source:
`mouse/contracts/gates/uic-00-semantic-inventory.md`.

UIC-01 remains blocked until UIC-00 is explicitly accepted.

## Dependency proof

Frozen UI source:

~~~text
repository: remappingbridge/mouse-ui
stable ref: release/ui-layout-v1.0
commit: e8adad7919e931c92515bf655ef4050876a8e7a9
product document: docs/product/ui-layout-v1.0.md
~~~

GitHub Actions dependency evidence:

~~~text
workflow: mouse-ui CI
run: 35682121924
head_sha: e8adad7919e931c92515bf655ef4050876a8e7a9
status: completed
conclusion: success
jobs:
  host-debug: success
  host-asan-ubsan: success
~~~

The stable branch `release/ui-layout-v1.0` resolves to the same commit.

## Implementation repository

~~~text
repository: remappingbridge/mouse
branch: uic/uic-00-semantic-inventory
rollback base: 7e238af7d2e4b642be9efb98bccf7c8711f04f84
candidate head: 5d1ac8edfd0c182e3fd8052826a7605bf22a1477
ahead of main: 3 commits
behind main: 0
~~~

Changed contract artifacts:

1. `contracts/ui-core/drafts/ui-layout-v1.0-semantic-boundary.md`
   - adds explicit UI-local/shared/Core-local ownership model;
   - adds summary traceability;
   - enumerates candidate shared data/actions/results;
   - proves UI-local and Core-local exclusions;
   - records compatibility consequences without freezing ABI.

2. `contracts/ui-core/traceability/uic-00-semantic-inventory.md`
   - exhaustive rule-level matrix;
   - 88 atomic frozen rules;
   - exact ownership classification for every row;
   - source coverage §1 through §16;
   - ownership proofs and cross-boundary cross-check.

3. `contracts/ui-core/drafts/uic-00-unresolved-decisions.md`
   - 23 explicitly deferred architecture/ABI decisions;
   - earliest intended future gate for each decision;
   - closed non-decisions from UIC-00.

No `mouse-ui` implementation, `mouse-core` implementation or frozen UI Layout 1.0 source was modified.

## Documentary/automated validation

A branch-content validation was executed against the candidate files.

Results:

~~~text
rule_rows: 88
unique_rule_ids: PASS
allowed_single_ownership: PASS
source_sections_1_to_16_covered: PASS
all_shared_rows_have_rationale: PASS
draft_has_data_actions_results: PASS
unresolved_list_present: PASS
no_c_binding_frozen: PASS
no_private_mui_type_normative: PASS
~~~

Additional repository check:

~~~text
compare main...uic/uic-00-semantic-inventory
status: ahead
commits: 3
changed files: 3
unexpected implementation files changed: 0
~~~

`remappingbridge/mouse` currently has no GitHub Actions workflow directory on main, so UIC-00 has no code build to execute. Its acceptance is documentary/architectural by gate definition.

## Acceptance checklist

Automated/documentary:

- [x] every frozen business rule has exactly one ownership classification
- [x] no private UI/Core implementation type is made normative
- [x] cross-boundary candidates are traceable to product behavior
- [x] open questions are explicitly enumerated

Human:

- [ ] architecture review accepts the ownership split and absence of accidental coupling

## Scope control

Performed:

- semantic inventory/matrix;
- updated contract draft/traceability;
- unresolved-decision list;
- UIC-00 execution evidence.

Not performed:

- C ABI freeze;
- Core implementation;
- UX/layout changes;
- transport-specific API design;
- UIC-01 work.

## Planner-spec note

The current UIC-00 gate document appears to have the labels/content of its **Deliverables** and **Forbidden scope** sections inverted: the Objective/Tasks/Acceptance require the semantic inventory while the two final sections state the opposite. This candidate follows the program README, gate Objective, Tasks and Acceptance criteria, which are mutually consistent. No later-gate ABI/Core/UX/transport work was performed.

## Known limitations / review focus

Human architecture review should specifically confirm:

- whether the UI reference capacity of 16 saved records is correctly treated as a shared semantic limit/capability candidate;
- whether display-name formatting remains UI-local while raw semantic name data crosses the boundary;
- whether profile mapping definitions belong in shared semantic contract rather than being implied only by profile names;
- whether Lock/Help themselves remain UI-local while abandonment/correlation semantics cross the boundary;
- whether the proposed division of Custom local draft vs confirmed persistent mapping is correct.

Until this review is accepted, UIC-00 is not accepted and UIC-01 is not eligible.
