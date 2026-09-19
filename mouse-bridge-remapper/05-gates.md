# Mouse Bridge Remapper gate plan

Status: **PLANNED ONLY — NO GATE EXECUTED**.

Gate IDs are fixed as `mbr-00`, `mbr-01`, ... . The executor must always start from the first incomplete dependency-complete gate. Completing a later gate never waives an earlier one.

## Sequence overview

| Gate | Purpose | Physical acceptance |
|---|---|---|
| mbr-00 | Freeze provenance, ambiguity decisions and canonical product/UX contract | No |
| mbr-01 | Clean repository bootstrap, module ownership and scope guards | No |
| mbr-02 | Host-pure interaction/state/projector + canonical screen/layout tests | No |
| mbr-03 | Waveshare renderer/HAT integration and physical layout/input acceptance | Yes |
| mbr-04 | Fixed Mouse-only USB identity and USB report ownership | Yes |
| mbr-05 | Canonical multi-source Mouse core + one BLE HOGP Mouse passthrough | Yes |
| mbr-06 | Migrate accepted G06 profiles, persistence, bonded reconnect and Logitech HID++ | Yes |
| mbr-07 | Multiple simultaneous BLE Mouse runtime + aggregation feasibility/qualification | Yes, critical |
| mbr-08 | Saved/new search policy, multi-Mouse registry, reconnect and removal transactions | Yes |
| mbr-09 | Complete new Mouse-only UX + per-Mouse profile targeting | Yes |
| mbr-10 | Resilience, reboot, corruption, disconnect and concurrency regression qualification | Yes |
| mbr-11 | Final release qualification and immutable accepted baseline | Yes |

---

# mbr-00 — Provenance, decisions and contract freeze

**Depends on:** none.

**Purpose:** create an implementation-ready contract without changing runtime code.

### Required work

- re-read `repo-planner/mouse-bridge-remapper/` at current main;
- verify BLU2USB accepted G06 SHA `7eee024ad4ee726c5a85ffa2f32b9f47187878af` still exists;
- record source tree/commit provenance for every G06 module/doc to be reused or studied;
- verify destination `mouse-bridge-remapper` base SHA and preserve `main` according to branch policy;
- resolve all BLOCKER ambiguities needed by mbr-01 through mbr-06, especially:
  - `AMB-001` Escape vs Mouse-only USB;
  - `AMB-002`/`003` focused Mouse/profile target;
  - `AMB-004` reconnect semantics if it affects early coordinator API;
  - `AMB-006` profile naming;
  - `AMB-007`/`008` didactic literal text/coordinates;
  - `AMB-012` Go-To-Home conflict;
  - `AMB-014` first-connected behavior;
  - `AMB-018` USB VID/PID/strings;
- freeze one canonical screen/control/transition table from `04-ux-state-model.md` and the verbatim requirements;
- freeze exact Mouse profile table after Escape decision;
- freeze exact USB identity contract after Escape decision;
- freeze expected minimum simultaneous-Mouse target for mbr-07 (must be at least 2);
- produce a migration manifest classifying G06 production files/modules as `REUSE-REFERENCE`, `PORT/ADAPT`, or `EXCLUDE`;
- explicitly record that G07+ Keyboard work is not a production base.

### Acceptance

Documentation/provenance only. No destination firmware feature, no build requirement, no UF2, no physical claim.

**Exit:** all decisions required to implement mbr-01..mbr-06 are explicit and no dependent gate needs to guess product behavior.

---

# mbr-01 — Clean bootstrap and architecture enforcement

**Depends on:** mbr-00.

**Purpose:** establish a clean Mouse-only repository skeleton and prevent obsolete BLU2USB boundaries from leaking into the new product.

### Required work

- dedicated `mbr/mbr-01-*` implementation branch from the approved destination base;
- host/Pico CMake composition and pinned toolchain/SDK inputs based on the accepted G01/G06 environment unless deliberately updated;
- materialize module boundaries from `03-target-architecture.md`;
- create architecture checks that prohibit:
  - Keyboard/Composite product modules;
  - `classic_hid`/`keyboard_transport`;
  - duplicate TinyUSB ownership;
  - raw BTstack outside runtime/adapters;
  - raw GPIO/SPI outside HAT/renderer adapters;
  - direct flash writes outside storage;
  - UI calling transports;
  - `.c` textual includes/macro interception patterns;
  - diagnostic CDC/UART-dependent product variants;
  - second Bluetooth lifecycle owner;
- establish host test target and Pico 2 W production scaffold;
- preserve no implementation behavior beyond a clean boot/scaffold.

### Automated evidence

- host configure/build;
- architecture guard suite;
- Pico 2 W production configure/build;
- non-empty UF2 structural check;
- exact source SHA/toolchain metadata.

### Physical acceptance

None unless bootstrap unexpectedly changes hardware assumptions. A generated scaffold UF2 is not product acceptance.

---

# mbr-02 — Interaction engine, UI projector and frozen screen model

**Depends on:** mbr-01.

**Purpose:** implement the complete new UX behavior as host-pure state/projection logic before hardware rendering or Bluetooth.

### Required work

- release-triggered HAT interaction state machine;
- lock/help/back/cancel semantics from mbr-00 decisions;
- canonical screen IDs and transition table;
- dynamic projections for Mouse name, `N OF M`, status and profile;
- focused-Mouse abstraction separated from runtime connection set;
- Pair/Saved/Retry commands emitted as semantic application commands only;
- all new screens represented in host model;
- Custom draft editing semantics;
- no old Keyboard/Composite pages;
- literal/golden row tests and exact token-coordinate assertions;
- name-length policy tests;
- selected-white/current-cyan priority tests;
- stale async transaction event filtering tests.

### Automated acceptance

Every canonical screen has deterministic projected rows/control map for representative states, all resolved character coordinates pass, and forbidden old screens are absent.

### Physical acceptance

None.

---

# mbr-03 — Waveshare renderer and HAT physical acceptance

**Depends on:** mbr-02.

**Purpose:** port/adapt accepted G03 renderer/HAT behavior to the new layouts without repeating pixel relocation and control-label regressions.

### Required work

- ST7789 240x240 backend for Waveshare Pico-LCD-1.3;
- accepted HAT GPIO mapping, active-low handling and debounce;
- retained G03 physical vertical relocation unless mbr-00 explicitly superseded it;
- exact new didactic horizontal coordinates;
- black/dark-magenta region rules and semantic colors;
- visible press feedback and release actions;
- Help ownership;
- lock/backlight-off and consumed unlock;
- rendering independent from Bluetooth availability;
- screenshot/framebuffer or renderer-command golden tests where practical.

### Physical closure

Executor supplies exact UF2, source SHA, UF2 SHA-256, board/toolchain metadata and numbered scenarios. Minimum scenarios must cover:

1. no-saved `searching-first` visual layout;
2. `first-mouse-connected` fixture/projection visual layout without claiming real Bluetooth connection;
3. saved-search/home fixtures;
4. all remapper screens with representative dynamic text;
5. Saved Devices pages/pagination;
6. Learn screen exact token positions;
7. press-white/release-restored behavior;
8. Help any-key return;
9. lock/unlock consumption;
10. long-name display policy and no clipping.

This gate validates UI/HAT; it does not claim Bluetooth or USB Mouse forwarding.

---

# mbr-04 — Fixed Mouse-only USB identity

**Depends on:** mbr-03 and resolved `AMB-001`/`AMB-018`.

**Purpose:** establish the stable host-facing USB product before live Bluetooth forwarding.

### Required work

- only `usb_mouse` owns TinyUSB descriptors/tasks/report submission;
- exact descriptor identity frozen by mbr-00;
- no Bluetooth-driven re-enumeration path;
- no CDC/MSC/MIDI/vendor-debug interface;
- Mouse report supports required buttons, X/Y, vertical wheel and horizontal pan as frozen by contract;
- report builder accepts canonical aggregated state, not BLE report bytes;
- test maximum/minimum relative chunking;
- production identity remains unchanged through UI lock/profile fixture transitions.

If mbr-00 chooses a synthetic Keyboard interface to retain Escape, this gate's title/scope must be explicitly amended before execution because that would no longer be strictly Mouse-only USB. Do not smuggle that interface into the implementation under the existing wording.

### Physical closure

Numbered scenarios verify host descriptor/interfaces/strings, movement-report fixture behavior where possible, stable enumeration through HAT navigation/lock, and absence of surprise CDC/debug interfaces.

---

# mbr-05 — Canonical Mouse core and single BLE HOGP passthrough

**Depends on:** mbr-04.

**Purpose:** reproduce the accepted G05 BLE Mouse path under multi-Mouse-capable internal boundaries, while physically validating one real Mouse first.

### Required work

- canonical `MouseSessionId`/`MouseSourceId`;
- source-aware button ownership aggregation;
- bounded relative X/Y/wheel/pan accumulation and TinyUSB-accepted consumption;
- one BTstack/CYW43 lifecycle owner;
- BLE HID discovery/security/bonding/HIDS Report Protocol;
- Report Map Mouse classification;
- canonical parser for buttons/X/Y/wheel/pan;
- duplicate Report-ID framing normalization and malformed-frame rejection;
- disconnect/overflow release safety;
- generic Mouse behavior independent of Logitech HID++;
- HAT/LCD remains responsive during Bluetooth activity;
- internal data structures must already support N sessions even if this gate opens only one physical connection.

### Automated acceptance

Include negative malformed-frame cases, duplicate down/up, disconnect-held-button, overflow cleanup, USB backpressure, stale generation rejection and architecture guards.

### Physical closure

Minimum numbered scenarios:

1. fresh BLE HOGP Mouse pairing;
2. movement;
3. left/right/middle click and hold/release;
4. vertical scroll;
5. horizontal pan if hardware supports it;
6. forward/back buttons if available;
7. disconnect while held with no stuck host button;
8. reconnect after disconnect;
9. UI remains responsive while moving/scrolling;
10. Mouse continues while LCD locked;
11. fixed USB identity does not change across connect/disconnect;
12. generic Mouse fail-safe.

---

# mbr-06 — G06 Mouse feature parity: profiles, persistence, reconnect and HID++

**Depends on:** mbr-05 and the resolved Escape contract.

**Purpose:** migrate every applicable, physically accepted Mouse behavior from BLU2USB G06 before adding simultaneous multi-Mouse runtime complexity.

### Required work

- Passthrough exact mapping;
- Default/Standard exact mapping;
- Escape mapping only if explicitly compatible with resolved product scope;
- Custom draft/edit/apply semantics;
- per-Mouse profile-kind model even though physical acceptance may use one connected Mouse in this gate;
- global CustomTemplate unless superseded;
- runtime+persistence-confirmed Apply feedback;
- versioned power-loss-safe dual-generation product storage;
- dirty/unapplied Custom draft reboot restoration;
- restore active profile before input becomes authoritative;
- BTstack credential separation;
- bonded BLE reconnect before generic fallback, bounded so absent saved peer cannot block forever;
- Logitech Lift HID++ Forward diversion/hold/release fix;
- return to Passthrough removes diversion;
- live connection/profile projection from runtime events;
- all relevant G03-G05 regressions.

### Physical closure

Re-run every applicable G06 scenario under the new product, including:

1. G05 passthrough regression;
2. Passthrough profile exactness;
3. Default/Standard exactness;
4. Escape exactness/hold-release **only if retained by mbr-00**;
5. Custom draft immediate reflection;
6. Custom complete apply;
7. profile transition while a mapped button is held;
8. generic/non-Logitech fail-safe;
9. Logitech Forward->Left held drag;
10. Passthrough restores native Forward;
11. remap continues while LCD locked;
12. stable USB identity;
13. active profile survives power cycle;
14. Custom template + dirty unapplied draft survive power cycle;
15. Logitech Lift bonded reconnect after Pico power cycle without fresh pairing;
16. generic bonded Mouse reconnect;
17. absent saved peer eventually yields to allowed generic/new search policy.

mbr-06 is the new stable **single-Mouse feature-parity baseline**. mbr-07 must branch from its accepted head.

---

# mbr-07 — Multiple simultaneous Mouse feasibility and runtime qualification

**Depends on:** mbr-06.

**Purpose:** prove the fundamental new requirement that several BLE HOGP mice can be connected and forwarded simultaneously, without destabilizing the accepted one-Mouse baseline.

This is a **critical feasibility gate**. Compilation or two saved records is not evidence of simultaneous runtime support.

### Required work

- multiple independent HIDS client/session contexts;
- coordinator can maintain more than one ready Mouse session;
- per-session Report Maps/parsers/security state/timers;
- per-session Logitech/vendor state;
- source-aware aggregator across mice;
- disconnect/reconnect one Mouse without disturbing another;
- profile kind independently associated with each saved Mouse;
- if global CustomTemplate retained, explicit multi-Mouse behavior tested;
- bounded queues/resource accounting;
- determine production simultaneous-Mouse maximum based on physical evidence/resources;
- no global `the_mouse`/single-connection handle assumptions.

### Mandatory automated tests

- Mouse A + Mouse B hold same target, release A, target remains held until B releases;
- Mouse A disconnect while B holds target;
- simultaneous relative motion accumulates deterministically;
- one session queue/parser failure releases only that session;
- stale callbacks from A generation N ignored after reconnect generation N+1;
- profile transition A does not mutate B;
- HID++ state A does not alter B;
- removal A does not release B;
- USB output remains fixed.

### Mandatory physical experiment

At least two independently identifiable BLE HOGP mice must be simultaneously ready and usable. Scenarios include:

1. connect Mouse A then Mouse B without A dropping;
2. move A and B alternately;
3. click/hold A while moving B;
4. both hold the same logical target, release one, verify target remains held from the other;
5. scroll from both;
6. disconnect/power off A while B continues;
7. reconnect A while B continues;
8. different profiles for A/B where supported by resolved UX targeting fixture;
9. Logitech HID++ Mouse plus generic Mouse coexistence if available;
10. lock/unlock display while both continue forwarding;
11. power/reconnect experiment appropriate to the current saved-search policy;
12. soak under high motion/report rate with no stuck state or UI starvation.

If two simultaneous mice cannot be made reliable within the chosen stack/resource envelope, stop. Do not proceed to UX polish while claiming the requirement exists.

---

# mbr-08 — Saved/new search, registry, reconnect and removal

**Depends on:** mbr-07 and resolved `AMB-004`, `AMB-010`, `AMB-011`, `AMB-016`, `AMB-021`.

**Purpose:** make persistent multi-Mouse lifecycle semantics match the new screens.

### Required work

- no-saved logically indefinite first search;
- finite saved-search transaction and timeout;
- explicit saved-search strategy for many saved mice;
- Pair New excludes/handles already-saved peers exactly as frozen;
- Pair New never destroys existing connected/saved mice on attempt failure;
- per-Mouse saved identity/name/profile/capability record;
- status projection for multiple connected mice;
- transactional remove with source release, disconnect, credential cleanup and product-state update;
- removal of last saved Mouse returns to first-search policy;
- removal of one among many preserves other live sessions/records;
- cancellation/stale completion isolation;
- reboot reconnection behavior matches frozen policy.

### Physical closure

Minimum scenarios cover zero->first Mouse, two or more saved, saved boot reconnect, timeout to home-retry, retry, Pair New success, Pair New with an already-saved peer present, Pair New timeout/retry, removal of connected non-last Mouse, removal of last Mouse, and power cycle after each major registry mutation.

---

# mbr-09 — Complete new UX integration and per-Mouse profile targeting

**Depends on:** mbr-08 and all UX/focus ambiguities resolved.

**Purpose:** connect the already-tested UI model to real runtime state and close every 2026-09-19 screen/transition requirement.

### Required work

- real `searching-first`/first-connected lifecycle;
- real home-searching/home-retry async transitions;
- real Pair New/retry/help transitions;
- defined multi-Mouse focus/selection mechanism;
- `home-connected` dynamic name/profile text from focused Mouse;
- remapper actions affect exactly the intended Mouse;
- Saved Devices `N OF M`, name, independent status and confirmed profile;
- transactional remove feedback/destination;
- Learn behavior;
- all Help screens;
- final lock map;
- final literal typo/wording normalization from mbr-00;
- no annotations such as `(nome do mouse)`, `(paginação)`, `(customizável)` rendered literally;
- no Keyboard/Composite UI remnants.

### Physical closure

Execute a full numbered screen/input matrix with at least two saved/connected mice where applicable, including each profile page, dynamic summary, pagination, Help, Back/Cancel, lock/unlock and async connection/disconnection while the relevant page is open.

---

# mbr-10 — Resilience and regression qualification

**Depends on:** mbr-09.

**Purpose:** treat the firmware as one integrated appliance and attack the failure boundaries that historically caused regressions.

### Required work / tests

- repeated power cycles with valid state;
- corrupt newest product slot -> fallback to previous valid generation;
- no valid product record -> safe defaults/first-search behavior;
- power interruption/fault-injection around persistence where testable;
- one Mouse disconnect while holding each mapped target;
- simultaneous mice owning same target;
- queue overflow/failure controls;
- repeated profile switches while buttons held;
- Pair New cancel/retry races;
- stale async connection completion after cancel;
- remove vs disconnect race;
- bonded reconnect with one saved peer absent and another available;
- Logitech HID++ unsupported/failure path;
- long Mouse names/unusual Report Maps/malformed reports;
- sustained report traffic while navigating/locking/rendering;
- USB host suspend/resume if supported by product contract;
- no USB identity drift/re-enumeration;
- production image contains no forbidden Keyboard/Composite/CDC feature according to final mbr-00 scope decision.

### Physical closure

Provide a focused resilience matrix and at least one extended multi-Mouse soak session. Any stuck button, unexplained disconnect loop, corrupted registry, frozen UI or USB re-enumeration keeps this gate open.

---

# mbr-11 — Final release qualification

**Depends on:** mbr-10.

**Purpose:** create the first immutable accepted Mouse Bridge Remapper baseline only after every prior gate is satisfied.

### Required work

- freeze exact source SHA/tree;
- clean production build from that committed revision;
- rerun all host/architecture/layout tests;
- rerun production Pico 2 W build;
- verify final descriptor and forbidden-feature checks;
- produce release candidate UF2 + SHA-256 + size + board/SDK/toolchain metadata;
- publish complete enumerated physical acceptance matrix covering:
  - boot with zero saved mice;
  - first pair;
  - saved reconnect;
  - Pair New;
  - several simultaneous mice;
  - each retained profile;
  - Custom editing/apply;
  - Logitech HID++ behavior;
  - per-Mouse profile targeting;
  - Saved Devices/remove-last/remove-one-of-many;
  - lock/help/navigation;
  - power-cycle persistence;
  - failure/reconnect safety;
  - fixed USB identity;
- operator reports every required scenario PASS on the exact candidate;
- create immutable acceptance record with exact artifact hashes and known limitations/capacity (including supported simultaneous-Mouse maximum).

No release/merge/tag is implied merely by preparing the candidate. Final repository integration policy must be explicitly followed at execution time.

## Universal gate rule

A physical gate is never “done” because CI is green. The executor must supply the exact `.uf2` and numbered scenarios, and the gate remains pending until the operator reports results for that exact source/artifact. A failed physical scenario keeps the same gate open; fix, rebuild, identify the new candidate, and rerun all scenarios affected by the change.
