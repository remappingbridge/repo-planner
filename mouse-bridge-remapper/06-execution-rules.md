# Gate execution and evidence rules

Status: **PLANNED ONLY**.

These rules apply whenever an `mbr-*` gate is later executed. They do not authorize execution now.

## 1. One gate at a time

- Select the first incomplete dependency-complete gate.
- Do not skip a blocked gate because a later feature is easier to build.
- Re-read the planning directory, current product documentation, predecessor acceptance record and destination repository before editing.
- Record exact base SHA before the first implementation change.
- Use an explicit `mbr/...` implementation branch; do not use destination `main` as scratch space.

## 2. Source authority

At every gate, revalidate:

1. current `repo-planner/mouse-bridge-remapper` planning;
2. current documentation in `tiagooliveirajs/mouse-bridge-remapper`;
3. accepted predecessor MBR SHA/artifact/evidence;
4. immutable BLU2USB G06 SHA `7eee024ad4ee726c5a85ffa2f32b9f47187878af` only for inherited/migration requirements;
5. destination current code/CI/toolchain state.

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

If implementation discovers a product contradiction, stop dependent work and update planning/documentation. Do not redefine behavior inside code comments or tests merely to make them pass.

## 4. Migration discipline

For G06-derived work:

- inspect exact accepted G06 source before porting;
- prefer behavior-preserving adaptation over blind copy;
- maintain a migration manifest recording source file/SHA and destination adaptation;
- preserve G06 release safety, persistence, reconnect, HID++ and UI lessons;
- **do not** carry forward simultaneous-Mouse machinery from the superseded MBR plan;
- never bring Bluetooth Keyboard/Composite modules into production;
- do not copy rejected G07 runtime architecture as a shortcut.

## 5. Single-live-Mouse architecture rule

Every implementation gate must preserve:

```text
saved_mice = 0..N persistent records
live_mouse = None | one ready MouseSession
```

Forbidden unless product documentation is explicitly changed first:

- two ready Mouse sessions at once;
- multi-HOGP live-session manager built for speculative future support;
- cross-Mouse held-button aggregation;
- multi-connected UI count/focus state;
- simultaneous-Mouse capacity qualification.

A candidate connection may not become authoritative before the outgoing live session has completed required release/disconnect cleanup.

## 6. HOME/search invariant

All gates touching lifecycle/UI must use the same HOME resolver:

```text
no saved mice -> searching-first
saved mice + live Mouse -> home-connected
saved mice + no live Mouse -> home-searching + automatic bounded saved search
```

If saved search expires, transition to `home-retry` / `DEVICE NOT FOUND`.

If the live Mouse disconnects/powers off and saved records remain, clear/release the session and invoke this same resolver. Do not add a hidden reconnect loop with different behavior.

## 7. Pair New replacement invariant

Pair New is not additive.

If a live Mouse exists:

1. stop accepting new events from it;
2. release held Mouse/Escape state;
3. disconnect/clear it;
4. preserve its saved record and bond;
5. start new-only search;
6. accept at most one winning unsaved Mouse.

A failed/canceled Pair New must not delete the previous saved Mouse and must not silently reconnect it. When HOME is later entered with no live Mouse, normal saved search applies.

## 8. Automated evidence

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

## 9. Physical evidence

Human/operator hardware evidence is required where `05-gates.md` says Yes.

The executor must provide in the same handoff:

- exact candidate source SHA;
- exact `.uf2` file;
- UF2 SHA-256 and size;
- board/SDK/toolchain metadata;
- enumerated scenarios with expected observable result;
- explicit predecessor regressions included.

The agent must not mark a physical scenario PASS unless the operator reports it.

If one scenario fails:

- the same gate stays open;
- diagnose/correct within gate scope;
- produce a new uniquely identified candidate;
- invalidate affected evidence;
- rerun failed and behaviorally impacted scenarios;
- do not advance.

## 10. High-risk regression triggers

Changes touching the following require focused predecessor regression:

- BTstack/CYW43 ownership/timing;
- HIDS session management;
- Report Map parsing/framing;
- held Mouse/Escape output state;
- USB descriptors/report submission;
- profile/remap engine;
- Logitech HID++;
- product storage/flash layout;
- Bluetooth credential handling;
- HOME/search/Pair New coordinator logic;
- UI navigation/lock/help;
- renderer geometry/color priority;
- device removal/reconnect policy.

## 11. Release safety

Production candidates require:

- committed clean source revision;
- no `-dirty` acceptance candidate;
- no diagnostic CDC/UART dependency;
- no hidden debug USB identity;
- no unapproved Bluetooth Keyboard/Composite module/interface;
- only the documented minimal USB Keyboard Escape output capability;
- no forced USB re-enumeration triggered by Bluetooth/profile/UI state;
- no more than one ready Mouse session;
- no unresolved blocker relevant to the candidate;
- all relevant predecessor regressions green;
- physical evidence where required.

## 12. Persistence safety

For gates that mutate product state:

- keep product state separate from BTstack credentials;
- use schema version/integrity checks;
- preserve a previous valid generation until the new generation is verified;
- test corrupt/torn newest-record fallback;
- test removal with power cycle;
- Pair New disconnects but never erases the old saved Mouse as a side effect;
- removing one disconnected saved Mouse must not disturb the current live Mouse;
- release the live Mouse before making its removal authoritative so the host cannot retain stuck output.

## 13. Held-state safety

Cross-Mouse aggregation is not needed, but held-state correctness remains mandatory.

Tests must include:

- duplicate Down/Up idempotence;
- two physical buttons from the current Mouse mapping to the same target;
- release one while the other remains held;
- disconnect/replacement while a target is held;
- profile change while held;
- synthetic Escape hold/release;
- parser/queue failure cleanup;
- stale old-session callback after replacement.

## 14. UI/layout safety

- Original 2026-09-19 rules remain provenance, but 2026-09-20 single-live-Mouse rules supersede conflicting multi-Mouse behavior.
- Final canonical text comes from the mbr-00 frozen screen table, never opportunistic renderer spelling correction.
- Dynamic annotations in parentheses are specification metadata, not display text.
- Exact token-column tests match the intended token, not an earlier coincidental character.
- New wording does not authorize reverting accepted physical pixel relocation.
- UI async transitions are driven by semantic runtime events and are host-testable.
- `home-connected` never has a multi-device count variant.
- at most one Saved Devices page can project connected/cyan.

## 15. No silent scope expansion

The following require a planning/documentation change before implementation:

- adding physical Bluetooth Keyboard transport;
- adding Bluetooth Composite support;
- adding Bluetooth Classic Mouse transport;
- allowing more than one live Mouse;
- reintroducing multi-connected UI/focus/capacity behavior;
- expanding USB Keyboard output beyond the documented synthetic Escape use;
- changing global CustomTemplate into per-Mouse Custom mappings;
- changing USB VID/PID/product strings after mbr-04 acceptance;
- changing profile mapping semantics;
- changing persistence schema incompatibly without migration/reset policy.

## 16. Gate completion report

Every completed gate leaves a durable report containing:

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
- deviations and authorization;
- exact next gate and entry conditions.

This makes the next execution independent from chat history.
