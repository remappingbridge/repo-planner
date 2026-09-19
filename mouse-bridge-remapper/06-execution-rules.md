# Gate execution and evidence rules

Status: **PLANNED ONLY**.

These rules apply whenever an `mbr-*` gate is later executed. They do not authorize execution now.

## 1. One gate at a time

- Select the first incomplete dependency-complete gate.
- Do not skip a blocked gate because a later feature is easier to build.
- Re-read the planning directory, current predecessor acceptance record and destination repository before editing.
- Record exact base SHA before the first implementation change.
- Use an explicit `mbr/...` implementation branch; do not use destination `main` as scratch space.

## 2. Source authority

At every gate, revalidate:

1. current `repo-planner/mouse-bridge-remapper` planning;
2. accepted predecessor MBR SHA/artifact/evidence;
3. immutable BLU2USB G06 SHA `7eee024ad4ee726c5a85ffa2f32b9f47187878af` only for inherited/migration requirements;
4. destination current code/CI/toolchain state.

G07+ BLU2USB Keyboard branches are evidence only and may not be imported wholesale.

## 3. Change discipline

Before implementation, the gate must state:

- objective;
- dependencies;
- exact base SHA;
- files/modules expected to change;
- relevant ambiguity decisions;
- inherited regressions at risk;
- out-of-scope work;
- verification commands;
- physical scenarios if applicable;
- rollback/recovery plan for persistent-state or descriptor changes.

If implementation discovers a product contradiction, stop the dependent work and update planning. Do not redefine behavior inside code comments or tests merely to make them pass.

## 4. Migration discipline

For G06-derived work:

- inspect the exact accepted G06 source before porting;
- prefer behavior-preserving adaptation over blind copy;
- maintain a migration manifest recording source file/SHA and destination adaptation;
- never assume G06 single-Mouse globals are safe for multi-Mouse;
- never bring Keyboard/Composite modules into production unless an explicit planning decision changes scope;
- do not copy rejected G07 runtime architecture as a shortcut.

## 5. Automated evidence

Every implementation gate records:

- source commit/tree;
- clean/dirty state;
- board target;
- Pico SDK version/revision;
- compiler/toolchain version;
- configure/build commands;
- host test commands/results;
- architecture/static checks;
- produced artifact path/name where applicable;
- UF2 size/SHA-256 where applicable;
- CI workflow/run/job/artifact identifiers when remote evidence is used.

A successful build alone is not behavioral acceptance.

## 6. Physical evidence

Human/operator hardware evidence is required exactly where `05-gates.md` says Yes.

The executor must provide, in the same handoff:

- exact candidate source SHA;
- exact `.uf2` file;
- UF2 SHA-256 and size;
- board/SDK/toolchain metadata;
- an enumerated scenario list with expected observable result for each item;
- explicit note of which predecessor regressions are included.

The agent must not mark a physical scenario PASS unless the operator reports it.

If one scenario fails:

- the same gate stays open;
- diagnose and correct within gate scope;
- produce a new uniquely identified candidate;
- invalidate evidence affected by the change;
- rerun the failed scenario plus all scenarios whose behavior could have changed;
- do not advance to the next gate.

## 7. High-risk regression triggers

Any change touching the following requires focused predecessor regression:

- BTstack/CYW43 ownership or timing;
- HIDS client/session management;
- Report Map parsing/framing;
- source ownership aggregation;
- USB descriptors/report submission;
- profile/remap engine;
- Logitech HID++;
- product storage/flash layout;
- Bluetooth credential handling;
- UI navigation/lock/help;
- renderer geometry/color priority;
- device removal/reconnect policy.

## 8. Release safety

Production candidate requirements:

- committed clean source revision;
- no `-dirty` acceptance candidate;
- no diagnostic CDC/UART dependency;
- no hidden debug USB identity;
- no unapproved Keyboard/Composite module/interface;
- no forced USB re-enumeration triggered by Bluetooth/profile/UI state;
- no unresolved BLOCKER ambiguity relevant to the candidate;
- all relevant predecessor regressions green;
- physical evidence attached where required.

## 9. Persistence safety

For gates that mutate product state:

- keep product state separate from BTstack credentials;
- use schema version/integrity checks;
- preserve a previous valid generation until the new generation is verified;
- test corrupt/torn newest record fallback;
- test removal with power cycle;
- never erase credentials/profile state for one Mouse as a side effect of pairing another Mouse;
- source-release a Mouse before/while making its removal authoritative so the host cannot retain a stuck button.

## 10. Multi-Mouse safety

Every gate after mbr-07 that touches runtime Mouse state must include regressions for at least two distinct source identities.

Mandatory invariant:

> No operation on Mouse A may release, overwrite, disconnect, remap, delete or reclassify Mouse B unless an explicit product transaction targets both.

Test this for button ownership, disconnect, reconnect, profile apply, HID++ state, removal and persistence restoration.

## 11. UI/layout safety

- Literal user requirements remain traceable to `requirements/2026-09-19-user-rules.md`.
- Final canonical text comes only from the mbr-00 decision table, never from opportunistic spelling correction in renderer code.
- Dynamic annotations in parentheses are specification metadata, not display text.
- Exact token-column tests must match the target token, not a coincidental character earlier in the row.
- New wording does not authorize reverting the accepted physical pixel relocation.
- UI asynchronous transitions are driven by semantic runtime events and must be testable without hardware.

## 12. No silent scope expansion

The following always require a planning change before implementation:

- adding any physical Keyboard transport;
- adding a USB Keyboard interface if mbr-00 chose strict Mouse-only USB;
- adding Composite pairing/support;
- adding Bluetooth Classic Mouse transport;
- changing the global CustomTemplate into per-Mouse Custom mappings;
- changing USB VID/PID/product strings after mbr-04 acceptance;
- increasing/decreasing the published simultaneous-Mouse capacity after release qualification;
- changing profile mapping semantics;
- changing persistence schema in a way that invalidates stored records without an explicit migration/reset policy.

## 13. Gate completion report

Every completed gate must leave a durable report containing:

- gate ID/status;
- objective achieved;
- predecessor accepted SHA;
- implementation SHA;
- changed files/modules;
- architecture/product decisions used;
- migrations/reuse provenance;
- automated evidence;
- physical evidence or `not required`;
- known limitations;
- deviations and why they are authorized;
- exact next gate and its entry conditions.

This makes the next execution independent from chat history.
