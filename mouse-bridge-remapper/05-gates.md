# Mouse Bridge Remapper gate plan

Status: **PLANNED ONLY — NO GATE EXECUTED**.

Gate IDs are fixed as `mbr-00`, `mbr-01`, ... . The executor always starts from the first incomplete dependency-complete gate. Completing a later gate never waives an earlier one.

The current product supports multiple saved mice but **only one connected Mouse at a time**. This plan intentionally removes all simultaneous-Mouse feasibility/capacity work.

## Sequence overview

| Gate | Purpose | Physical acceptance |
|---|---|---|
| mbr-00 | Freeze provenance, remaining decisions and canonical product/UX contract | No |
| mbr-01 | Clean repository bootstrap, module ownership and architecture guards | No |
| mbr-02 | Host-pure interaction/state/projector + canonical screen/layout tests | No |
| mbr-03 | Waveshare renderer/HAT integration and physical layout/input acceptance | Yes |
| mbr-04 | Fixed USB Mouse + synthetic Escape output identity | Yes |
| mbr-05 | Canonical single-session Mouse core + one BLE HOGP Mouse passthrough | Yes |
| mbr-06 | Migrate accepted G06 profiles, persistence, bonded reconnect and Logitech HID++ | Yes |
| mbr-07 | Saved/new search, one-live-Mouse replacement, registry/reconnect/removal | Yes |
| mbr-08 | Complete new UX integration against real runtime state | Yes |
| mbr-09 | Resilience, reboot, corruption, disconnect and race regression qualification | Yes |
| mbr-10 | Final release qualification and immutable accepted baseline | Yes |

---

# mbr-00 — Provenance, decisions and contract freeze

**Depends on:** none.

**Purpose:** produce an implementation-ready contract without changing runtime code.

### Required work

- re-read `repo-planner/mouse-bridge-remapper/` at current main;
- re-read current product documentation in `tiagooliveirajs/mouse-bridge-remapper`;
- verify BLU2USB accepted G06 SHA `7eee024ad4ee726c5a85ffa2f32b9f47187878af` still exists;
- record source tree/commit provenance for every G06 module/doc to be reused or studied;
- verify destination base SHA and branch policy;
- explicitly freeze the already-decided architecture rules:
  - zero or one live Mouse;
  - multiple saved mice;
  - Pair New disconnects/releases current Mouse first but keeps it saved;
  - HOME with saved mice + no live Mouse starts bounded saved search automatically;
  - disconnect/power-off of live Mouse uses the same HOME search flow;
  - saved-search timeout -> `DEVICE NOT FOUND`;
  - Escape retained via minimal fixed USB Keyboard output only;
  - no Bluetooth Keyboard/Composite product support;
- resolve remaining blockers/material UX decisions from `02-ambiguity-register.md`, especially:
  - final USB VID/PID/manufacturer/product strings;
  - Default vs Standard visible naming;
  - exact didactic title/coordinates;
  - Pair New treatment of already-saved candidates;
  - `BACK TRY SAVED` exact navigation;
  - `JOY LEFT: GO TO HOME` decision;
  - lock availability by screen;
  - first-connected controls;
  - disconnected Saved Devices word;
  - long-name display policy;
  - search timeout constants;
  - BLE HOGP-only Mouse transport ratification;
- freeze one canonical screen/control/transition table from current product docs;
- freeze exact Mouse profile table;
- freeze exact USB identity/descriptor contract;
- produce migration manifest classifying G06 modules as `REUSE-REFERENCE`, `PORT/ADAPT`, or `EXCLUDE`;
- explicitly classify simultaneous-Mouse infrastructure as `EXCLUDE` rather than “future work”;
- explicitly record G07+ Keyboard work as research evidence only.

### Acceptance

Documentation/provenance only. No destination firmware feature, no build requirement, no UF2 and no physical claim.

**Exit:** all decisions required to implement mbr-01..mbr-07 are explicit and no dependent gate needs to guess product behavior.

---

# mbr-01 — Clean bootstrap and architecture enforcement

**Depends on:** mbr-00.

**Purpose:** establish a clean repository skeleton that encodes the current single-live-Mouse product boundaries.

### Required work

- dedicated `mbr/mbr-01-*` branch from approved destination base;
- host/Pico CMake composition and pinned toolchain/SDK inputs based on accepted G01/G06 environment unless deliberately updated;
- materialize module boundaries from `03-target-architecture.md`, including conceptually:
  - `domain`;
  - `mouse_registry`;
  - `mouse_session`;
  - `output_state`;
  - `profiles`/`remap`;
  - `pairing_coordinator`;
  - `bt_runtime` / `ble_hogp` / `logitech_hidpp`;
  - `product_storage`;
  - `usb_hid`;
  - interaction/UI/renderer/HAT/app;
- architecture checks prohibit:
  - more than one ready Mouse session;
  - simultaneous-Mouse session managers/aggregators added as speculative future-proofing;
  - Keyboard/Composite product modules;
  - `classic_hid` / `keyboard_transport`;
  - duplicate TinyUSB ownership;
  - raw BTstack outside runtime/adapters;
  - raw GPIO/SPI outside HAT/renderer adapters;
  - direct flash writes outside storage;
  - UI calling transports;
  - `.c` textual includes/macro interception;
  - diagnostic CDC/UART-dependent product variants;
  - second Bluetooth lifecycle owner;
- establish host test target and Pico 2 W production scaffold.

### Automated evidence

- host configure/build;
- architecture guard suite;
- Pico 2 W production configure/build;
- non-empty UF2 structural check;
- exact source SHA/toolchain metadata.

### Physical acceptance

None. A scaffold UF2 is not product acceptance.

---

# mbr-02 — Interaction engine, UI projector and frozen screen model

**Depends on:** mbr-01.

**Purpose:** implement the complete UX state/projection logic as host-pure code before hardware rendering or Bluetooth.

### Required work

- release-triggered HAT interaction state machine;
- lock/help/back/cancel semantics from mbr-00;
- canonical screen IDs and transition table;
- one HOME resolver:
  - no saved -> `searching-first`;
  - saved + live -> `home-connected`;
  - saved + no live -> `home-searching` + semantic start-saved-search command;
- saved-search timeout -> `home-retry`;
- live disconnect -> immediate `home-searching` projection + saved-search command;
- Pair New semantic replacement flow; host model never projects two live mice;
- dynamic projections for Mouse name, `N OF M`, status/profile and Custom mappings;
- all new screens represented;
- no old Keyboard/Composite or multi-connected count/focus state;
- literal/golden row tests and exact token-coordinate assertions;
- name-length policy tests;
- selected-white/current-cyan priority tests;
- stale async transaction/session filtering tests.

### Automated acceptance

Every canonical screen has deterministic projected rows/control map for representative states; all final character coordinates pass; forbidden states/screens are absent; HOME and Pair New transitions satisfy the single-live invariant.

### Physical acceptance

None.

---

# mbr-03 — Waveshare renderer and HAT physical acceptance

**Depends on:** mbr-02.

**Purpose:** port/adapt accepted G03 renderer/HAT behavior to the new layouts without repeating pixel relocation or control-label regressions.

### Required work

- ST7789 240x240 backend for Waveshare Pico-LCD-1.3;
- accepted HAT GPIO mapping, active-low handling and debounce;
- retained G03 physical vertical relocation unless explicitly superseded;
- exact current didactic horizontal coordinates;
- black/dark-magenta region rules and semantic colors;
- visible press feedback and release actions;
- Help ownership;
- lock/backlight-off and consumed unlock;
- rendering independent from Bluetooth availability;
- framebuffer/renderer-command golden tests where practical.

### Physical closure

Executor supplies exact UF2, source SHA, UF2 SHA-256, board/toolchain metadata and numbered scenarios. Minimum scenarios:

1. no-saved `searching-first` visual layout;
2. `first-mouse-connected` fixture visual layout;
3. `home-searching` / `home-retry` / `home-connected` fixtures;
4. all remapper screens;
5. Saved Devices pagination and connected-name cyan fixture;
6. Learn exact token positions;
7. press-white/release-restored behavior;
8. Help any-key return;
9. lock/unlock consumption;
10. long-name policy and no clipping;
11. no multi-device count screen/state.

This gate validates UI/HAT only; it does not claim Bluetooth or USB forwarding.

---

# mbr-04 — Fixed USB Mouse + synthetic Escape identity

**Depends on:** mbr-03 and frozen USB identity from mbr-00.

**Purpose:** establish stable host-facing USB behavior before live Bluetooth forwarding.

### Required work

- only `usb_hid` owns TinyUSB descriptors/tasks/report submission;
- exact VID/PID/manufacturer/product strings frozen by mbr-00;
- fixed Mouse HID output;
- minimal Keyboard HID output sufficient only for documented synthetic Escape;
- no Bluetooth-driven `tud_disconnect()/tud_connect()` re-enumeration;
- no CDC/MSC/MIDI/vendor-debug interface;
- Mouse report supports required buttons, X/Y, vertical wheel and horizontal pan;
- report builder accepts canonical output state, never BLE report bytes;
- Escape report builder accepts canonical synthetic held state;
- test relative min/max chunking and held-state backpressure;
- production identity remains unchanged through UI/profile fixtures.

### Physical closure

Numbered scenarios verify descriptors/interfaces/strings, stable enumeration, Mouse report behavior from fixtures where possible, Escape press/hold/release from fixtures, lock/navigation stability and absence of surprise debug interfaces.

---

# mbr-05 — Canonical single-session Mouse core and BLE HOGP passthrough

**Depends on:** mbr-04.

**Purpose:** reproduce accepted G05 BLE Mouse behavior under the final one-live-session architecture.

### Required work

- `MouseId` / `MouseSessionId` with generation token;
- one optional authoritative live session;
- held output state within the current session, including multiple physical sources mapping to one target;
- bounded relative X/Y/wheel/pan accumulation and TinyUSB-accepted consumption;
- one BTstack/CYW43 lifecycle owner;
- BLE HID discovery/security/bonding/HIDS Report Protocol;
- Report Map Mouse classification;
- canonical parser for buttons/X/Y/wheel/pan;
- duplicate Report-ID framing normalization and malformed-frame rejection;
- disconnect/overflow/parser-failure release safety;
- stale-generation event rejection;
- generic Mouse behavior independent of Logitech HID++;
- HAT/LCD responsiveness during Bluetooth activity;
- no second ready Mouse path exists.

### Automated acceptance

Include malformed frames, duplicate Down/Up, two current-session physical sources mapped to same target, disconnect-held-button, overflow cleanup, USB backpressure, stale generation rejection and architecture guards.

### Physical closure

Minimum scenarios:

1. fresh BLE HOGP Mouse pairing;
2. movement;
3. left/right/middle click and hold/release;
4. vertical scroll;
5. horizontal pan if hardware supports it;
6. forward/back buttons if available;
7. disconnect while held with no stuck host output;
8. reconnect after disconnect;
9. UI remains responsive while moving/scrolling;
10. Mouse continues while LCD locked;
11. fixed USB identity does not change across connect/disconnect;
12. generic Mouse fail-safe.

---

# mbr-06 — G06 feature parity: profiles, persistence, reconnect and HID++

**Depends on:** mbr-05.

**Purpose:** migrate every applicable physically accepted Mouse behavior from BLU2USB G06 into the new single-live-session product.

### Required work

- Passthrough exact mapping;
- Default/Standard exact mapping;
- Escape exact mapping through synthetic USB Escape;
- Custom draft/edit/apply semantics;
- per-saved-Mouse confirmed profile kind;
- global CustomTemplate unless superseded;
- runtime+persistence-confirmed Apply feedback;
- versioned power-loss-safe dual-generation product storage;
- dirty/unapplied Custom draft reboot restoration;
- restore selected Mouse profile before its input becomes authoritative;
- BTstack credential separation;
- bounded bonded reconnect/fallback;
- Logitech Lift HID++ Forward diversion/hold/release fix;
- Passthrough removes unneeded diversion;
- live connection/profile projection from runtime events;
- relevant G03-G05 regressions.

### Physical closure

Re-run every applicable G06 scenario, including:

1. mbr-05 passthrough regression;
2. Passthrough profile exactness;
3. Default/Standard exactness;
4. Escape exactness and hold/release;
5. Custom draft immediate reflection;
6. Custom full Apply;
7. profile transition while mapped output is held;
8. generic/non-Logitech fail-safe;
9. Logitech Forward->Left held drag;
10. Passthrough restores native Forward;
11. remap continues while LCD locked;
12. stable USB identity;
13. active profile survives power cycle;
14. Custom template + dirty unapplied draft survive power cycle;
15. Logitech Lift bonded reconnect after Pico power cycle without fresh pairing;
16. generic bonded Mouse reconnect;
17. absent saved peer yields to bounded timeout behavior rather than trapping runtime.

mbr-06 is the stable feature-parity baseline for lifecycle integration.

---

# mbr-07 — Saved/new search, replacement, registry, reconnect and removal

**Depends on:** mbr-06 and relevant mbr-00 decisions.

**Purpose:** implement the final multiple-saved/one-live lifecycle contract.

### Required work

- persistent registry of multiple saved mice;
- no-saved logically indefinite first search;
- HOME entry with saved mice + no live session automatically starts bounded saved search;
- saved search considers eligible saved identities but accepts only the first successful ready Mouse;
- saved search timeout -> `home-retry` / `DEVICE NOT FOUND`;
- live Mouse disconnect/power-off -> release/clear session -> HOME resolver -> automatic saved search;
- `KEY A: RETRY SEARCH` starts a fresh bounded saved search;
- Pair New is new-only and accepts one winner;
- Pair New while connected performs safe release/disconnect first while preserving old SavedMouse record/bond;
- Pair New failure/cancel leaves old Mouse saved but disconnected;
- returning to HOME with no live Mouse triggers normal saved search;
- already-saved candidates in Pair New follow the exact frozen mbr-00 policy;
- per-saved-Mouse name/profile/capability state;
- at most one Saved Devices page can project connected/cyan;
- transactional remove with held-state release, live disconnect if applicable, credential cleanup and product-state update;
- removal of last saved Mouse returns to `searching-first` + first search;
- removal of a disconnected saved Mouse does not disturb current live Mouse;
- stale/canceled transaction completion isolation;
- power-cycle behavior matches the same HOME resolver.

### Automated acceptance

Host/integration tests cover:

- one-live invariant through all coordinator transitions;
- saved search one-winner semantics;
- saved-search timeout;
- disconnect -> automatic HOME search;
- Pair New current-Mouse teardown ordering;
- Pair New failure preserves saved record/bond model;
- stale Pair New completion after cancel ignored;
- removal connected vs disconnected;
- removal last vs non-last;
- no second ready session can be published.

### Physical closure

Minimum scenarios:

1. zero saved -> first Mouse pair;
2. two or more mice saved over repeated Pair New operations;
3. boot with saved Mouse available -> reconnect;
4. boot with all saved mice absent -> bounded search -> `DEVICE NOT FOUND`;
5. `KEY A` retry then successful reconnect;
6. connected Mouse powered off -> automatic `home-searching` -> another saved Mouse connects if available;
7. connected Mouse powered off with no other reachable saved Mouse -> `DEVICE NOT FOUND` after timeout;
8. Pair New while Mouse A connected: A disconnects, Mouse B pairs and becomes sole live Mouse;
9. Pair New failure: A remains saved, no live Mouse, returning HOME starts saved search and can reconnect A;
10. Pair New with already-saved peer present follows frozen policy;
11. remove disconnected non-last Mouse without disturbing live Mouse;
12. remove connected non-last Mouse then HOME saved search behavior;
13. remove last saved Mouse -> `searching-first`;
14. power cycle after major registry mutations.

---

# mbr-08 — Complete new UX integration

**Depends on:** mbr-07 and all remaining UX decisions frozen.

**Purpose:** connect the host-tested UI model to real runtime state and close every current screen/transition requirement.

### Required work

- real `searching-first` / first-connected lifecycle;
- real HOME resolver on boot/navigation/unlock/disconnect;
- real `home-searching` / `home-retry` async transitions;
- real Pair New replacement/retry/help transitions;
- `home-connected` dynamic name/profile from the sole live Mouse;
- remapper actions affect exactly that Mouse;
- Saved Devices `N OF M`, one connected/cyan page maximum, confirmed profiles and final disconnected wording;
- transactional remove feedback/destination;
- Learn behavior;
- all Help screens;
- final lock map;
- final literal/coordinate normalization;
- no annotation metadata rendered literally;
- no Keyboard/Composite or multi-connected UI remnants.

### Physical closure

Execute a complete numbered screen/input matrix covering:

- zero saved;
- one/many saved;
- live connected;
- disconnected and searching;
- search timeout/retry;
- Pair New replacement success/failure;
- each profile page;
- dynamic summary;
- Saved Devices pagination/status/colors;
- Help;
- Back/Cancel;
- lock/unlock;
- async connect/disconnect while relevant pages are visible.

---

# mbr-09 — Resilience and regression qualification

**Depends on:** mbr-08.

**Purpose:** attack integrated failure boundaries and ensure the simpler architecture still preserves all safety guarantees.

### Required work / tests

- repeated power cycles with valid state;
- corrupt newest product slot -> fallback to previous valid generation;
- no valid product record -> safe defaults/first-search behavior;
- power interruption/fault injection around persistence where testable;
- live Mouse disconnect while each mapped target/Escape is held;
- two physical source buttons from one Mouse mapped to same target, release one then the other;
- queue/parser failure release controls;
- repeated profile switches while held;
- Pair New cancel/retry races;
- stale connection completion after cancel/replacement;
- replacement ordering prevents two ready sessions;
- remove vs disconnect race;
- saved search with one/many absent saved peers;
- connected Mouse power-off -> automatic saved search -> timeout or alternate saved reconnect;
- Logitech HID++ unsupported/failure path;
- long Mouse names/unusual Report Maps/malformed reports;
- sustained report traffic while navigating/locking/rendering;
- USB host suspend/resume if in product contract;
- no USB identity drift/re-enumeration;
- no forbidden Keyboard/Composite/debug feature;
- no simultaneous-Mouse machinery or state leak.

### Physical closure

Provide a focused resilience matrix and at least one extended single-Mouse soak including repeated disconnect/reconnect and Pair New replacement cycles. Any stuck output, false connected UI, endless search outside first-search policy, corrupted registry, frozen UI or USB re-enumeration keeps this gate open.

---

# mbr-10 — Final release qualification

**Depends on:** mbr-09.

**Purpose:** create the first immutable accepted Mouse Bridge Remapper baseline only after every prior gate is satisfied.

### Required work

- freeze exact source SHA/tree;
- clean production build from that committed revision;
- rerun all host/architecture/layout tests;
- rerun production Pico 2 W build;
- verify final USB descriptor and forbidden-feature checks;
- produce release candidate UF2 + SHA-256 + size + board/SDK/toolchain metadata;
- publish complete enumerated physical acceptance matrix covering:
  - boot with zero saved mice;
  - first pair;
  - multiple saved records;
  - saved reconnect;
  - connected Mouse power-off -> automatic saved search;
  - saved search timeout -> `DEVICE NOT FOUND`;
  - Retry Search;
  - Pair New replacement success and failure recovery;
  - each retained profile;
  - Custom editing/apply;
  - Logitech HID++ behavior;
  - Saved Devices/remove-last/remove-non-last;
  - lock/help/navigation;
  - power-cycle persistence;
  - failure/reconnect safety;
  - fixed USB Mouse + synthetic Escape identity;
  - proof that no two Mouse sessions become ready simultaneously;
- operator reports every required scenario PASS on the exact candidate;
- create immutable acceptance record with exact artifact hashes and known limitations.

No release/merge/tag is implied merely by preparing the candidate. Final repository integration policy must be explicitly followed at execution time.

## Universal gate rule

A physical gate is never complete because CI is green. The executor must provide the exact `.uf2` and numbered scenarios, and the gate remains pending until the operator reports results for that exact source/artifact.

A failed physical scenario keeps the same gate open: fix, rebuild, identify the new candidate, invalidate affected evidence and rerun all behavior that could have changed.
