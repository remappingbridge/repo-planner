# Mouse Bridge Remapper gate plan

Current plan after accepted MBR-01 clean bootstrap.

## Sequence status

| Gate | Status | Purpose | Physical acceptance |
|---|---|---|---|
| mbr-00 | **COMPLETE / ACCEPTED** | provenance, decisions, canonical contract freeze | No |
| mbr-01 | **COMPLETE / ACCEPTED** | clean bootstrap, module ownership, architecture guards | No |
| mbr-02 | **NEXT / PLANNED** | host-pure interaction/state/projector + golden UX tests | No |
| mbr-03 | PLANNED | Waveshare renderer/HAT physical acceptance | Yes |
| mbr-04 | PLANNED | fixed USB Mouse + synthetic Escape identity | Yes |
| mbr-05 | PLANNED | canonical single-session BLE HOGP Mouse passthrough | Yes |
| mbr-06 | PLANNED | G06 profiles/persistence/reconnect/HID++ parity | Yes |
| mbr-07 | PLANNED | saved/new search, Pair New handoff, registry/reconnect/removal | Yes |
| mbr-08 | PLANNED | complete real UX integration | Yes |
| mbr-09 | PLANNED | resilience/regression qualification | Yes |
| mbr-10 | PLANNED | final release qualification | Yes |

Executor always selects the first incomplete dependency-complete gate. After accepted mbr-01, the first executable gate is mbr-02.

---

# mbr-00 — Provenance, decisions and contract freeze

**Status: COMPLETE / ACCEPTED.**

**Accepted output:** see `07-mbr-00-frozen-contract.md`, `08-mbr-00-migration-manifest.md`, and `executions/mbr-00/completion.md`.

**No firmware implementation, build, UF2 or physical acceptance was required or performed.**

Frozen decisions include:

- BLE HOGP Mouse only;
- multiple saved Mouse records, <=1 authoritative live Mouse;
- Pair New keeps current healthy Mouse live while qualifying a new-only replacement candidate, then performs safe handoff;
- saved candidates ignored by Pair New;
- HOME resolver and 8-second saved-search behavior;
- first-Mouse 8-second repeated cycles;
- Pair New 15-second window;
- exact new Pair New Help screens;
- STANDARD canonical profile vocabulary;
- Escape via minimal fixed USB Keyboard output only;
- exact project USB identity/interface shape;
- exact didactic controls/coordinates;
- no hidden Lock controls;
- `STATUS: DISCONNECTED`;
- deterministic 21-character Mouse-name projection;
- intentional `JOY LEFT: GO TO HOME` on `escape-active`.

---

# mbr-01 — Clean bootstrap and architecture enforcement

**Status: COMPLETE / ACCEPTED.**

**Depends on:** accepted mbr-00.

**Accepted product implementation head:** `826c50dab3b105c6bcecef6a6dbba401506aefc9`.

**Integrated product main:** `cee10ee157ce0d7f6655df203327422c3c40e6b3` via PR #1.

**Exact-head acceptance CI:** run `35477941473`, with both `host-architecture` and `pico2-w-production` successful.

**Structural UF2:** 12288 bytes, SHA-256 `3110c90819a67eab761d75fae8ab396823e343dc3a0ea3ac16e9868c5155d452`; scaffold/build evidence only, not behavioral or physical acceptance.

**Durable execution evidence:** `executions/mbr-01/pre-implementation.md` and `executions/mbr-01/completion.md`.

Accepted implementation established:

- host/Pico 2 W CMake composition with G06-derived pinned SDK/toolchain inputs;
- all frozen conceptual modules materialized;
- one authoritative Mouse slot plus a separate non-authoritative Pair New candidate representation;
- exact module/dependency facade;
- architecture guards against Keyboard/Composite/Classic-HID/simultaneous-authoritative-Mouse scope;
- one BT lifecycle owner (`bt_runtime`) and one future TinyUSB owner (`usb_hid`) encoded as guard boundaries;
- no G07 multicore/Core1 architecture;
- no `.c` textual includes, transport/HAL macro interception, forced USB re-enumeration or diagnostic production stdio/CDC;
- host bootstrap tests and architecture tests;
- Pico 2 W production scaffold build under SDK 2.2.0 and ARM GCC 13.2.1.

No UI behavior, renderer/HAT behavior, final USB HID behavior, real BLE forwarding, profiles/persistence/HID++, saved/new lifecycle or physical acceptance is implied by mbr-01.

---

# mbr-02 — Interaction engine, UI projector and golden screen model

**Depends on:** mbr-01.

**Purpose:** implement frozen UX as host-pure state/projection logic before real renderer/Bluetooth.

### Required work

- release-triggered interaction;
- frozen screen IDs/literal rows/control maps/transitions;
- unified HOME resolver;
- FIRST_MOUSE/SEARCH_SAVED/PAIR_NEW semantic commands;
- Pair New model with current authoritative Mouse + optional non-authoritative replacement candidate;
- stale transaction/session event filtering;
- frozen timings as policy constants;
- name truncation/fallback;
- STANDARD vocabulary;
- Saved Devices CONNECTED/DISCONNECTED/cyan behavior;
- Custom draft semantics;
- exact new Pair New Help screens;
- didactic B/X/Y behavior and token columns;
- intentional Escape GO TO HOME;
- no Keyboard/Composite/multi-connected states.

### Automated acceptance

Golden tests cover every canonical screen, transition and representative async state. No physical acceptance.

---

# mbr-03 — Waveshare renderer and HAT physical acceptance

**Depends on:** mbr-02.

**Purpose:** port/adapt accepted G03 renderer/HAT behavior to frozen MBR UX.

### Required work

- ST7789 240x240 Waveshare Pico-LCD-1.3 backend;
- accepted GPIO/active-low/debounce behavior;
- retained physical vertical relocation;
- exact MBR-00 horizontal token columns;
- black/dark-magenta regions and semantic colors;
- press feedback/release actions;
- Help ownership;
- ordinary Lock and instructional B/X/Y behavior;
- framebuffer/renderer-command golden tests where practical.

### Physical closure

Provide exact UF2/source SHA/hash/toolchain and numbered scenarios covering all canonical screen families, exact Pair New Help text, didactic coordinates, colors, Help, Lock/unlock, HOME shortcut and long-name policy.

---

# mbr-04 — Fixed USB Mouse + synthetic Escape identity

**Depends on:** mbr-03.

**Purpose:** establish the MBR-00 frozen host-facing USB product before live BLE forwarding.

### Required work

- `usb_hid` sole TinyUSB owner;
- VID `0xCAFE`, PID `0x4011`, bcdDevice `0x0100`;
- manufacturer `tiagooliveirajs`, product `Mouse Bridge Remapper`, no serial;
- interface 0 HID Mouse;
- interface 1 minimal HID Keyboard used only for synthetic Escape;
- no CDC/debug interface;
- no Bluetooth-driven re-enumeration;
- Mouse report supports five buttons, X/Y, wheel, pan;
- synthetic Escape press/hold/release;
- report builders consume canonical state only.

### Physical closure

Verify exact host descriptors/interfaces/strings, stable enumeration, Mouse report fixtures and Escape output; no surprise debug interface.

---

# mbr-05 — Canonical Mouse core and one BLE HOGP passthrough

**Depends on:** mbr-04.

**Purpose:** reproduce accepted G05 BLE Mouse path under single-authoritative-session architecture.

### Required work

- `MouseSessionId` generation/stale-callback protection;
- current-session held target ownership/refcounts;
- bounded X/Y/wheel/pan accumulation + USB backpressure semantics;
- one CYW43/BTstack owner;
- BLE discovery/security/bonding/HIDS Report Protocol;
- Report Map Mouse classification;
- canonical buttons/X/Y/wheel/pan parser;
- duplicated Report-ID normalization and malformed rejection;
- disconnect/overflow release safety;
- generic Mouse behavior independent of HID++;
- UI remains responsive/lock does not pause Mouse;
- no Bluetooth Keyboard/Composite support.

### Physical closure

Fresh BLE HOGP pairing, movement/buttons/holds, scroll/pan, Forward/Back if available, disconnect-held cleanup, reconnect, LCD navigation/lock responsiveness, fixed USB identity and generic fail-safe.

---

# mbr-06 — G06 Mouse feature parity

**Depends on:** mbr-05.

**Purpose:** migrate every applicable physically accepted G06 Mouse behavior before new lifecycle UX is integrated.

### Required work

- exact Passthrough/Standard/Escape mappings;
- Custom draft/edit/apply behavior;
- per-saved-Mouse confirmed profile kind;
- global Custom template;
- runtime+persistence-confirmed success;
- versioned dual-generation product storage/integrity fallback;
- dirty/unapplied Custom draft restoration;
- product state vs BT credentials separation;
- bounded bonded reconnect mechanics reused by SEARCH_SAVED;
- Logitech Lift HID++ Forward hold/release fix;
- Passthrough removes diversion;
- live connection/profile projection;
- all relevant G03-G05 regressions.

### Physical closure

Re-run applicable G06 Mouse/profile/persistence/reconnect/HID++ scenarios on the exact MBR candidate, including power cycles and generic Mouse fallback.

---

# mbr-07 — Saved/new lifecycle, Pair New handoff, registry and removal

**Depends on:** mbr-06.

**Purpose:** implement frozen MBR lifecycle semantics over the accepted one-Mouse feature baseline.

### Required work

- FIRST_MOUSE repeated 8-second cycles;
- HOME SEARCH_SAVED 8-second transaction;
- saved-search first-winner semantics;
- timeout/cancel -> `DEVICE NOT FOUND`;
- Pair New 15-second new-only search;
- current Mouse remains authoritative/usable during Pair New discovery;
- already-saved candidates ignored for Pair New acceptance;
- non-authoritative replacement candidate state;
- atomic release-safe handoff to one unsaved winner;
- timeout/cancel before handoff leaves old Mouse live;
- user unplugging old Mouse during Pair New does not change Pair New purpose;
- HOME later resolves no-live state to saved search;
- persistent multiple-saved registry;
- transactional remove/credential cleanup;
- last saved removal -> first search;
- stale completion isolation.

### Physical closure minimum

1. zero saved -> first pair;
2. boot with saved Mouse -> saved reconnect;
3. absent saved Mouse -> 8s `DEVICE NOT FOUND`;
4. Retry search;
5. connected Mouse powered off on HOME -> automatic saved search;
6. connected Mouse powered off on non-HOME page -> no false connected state; HOME access starts search;
7. Pair New while Mouse A works: A continues during discovery;
8. saved candidate present during Pair New: ignored as new winner;
9. Pair New timeout/cancel: A remains connected;
10. Pair New success A->B: no stuck output, B sole live Mouse, A remains saved;
11. Pair New Help flow: unplug A, Back until `SEARCHING SAVED MOUSE`, saved search begins;
12. removal of disconnected saved Mouse without disturbing live Mouse;
13. removal of live Mouse;
14. removal of last saved Mouse -> first search;
15. power cycle after major registry mutations.

---

# mbr-08 — Complete real UX integration

**Depends on:** mbr-07.

**Purpose:** bind host-tested UI model to real runtime and close full screen/control contract.

### Required work

- all canonical screens against real state;
- exact help text/literal rows;
- async connect/disconnect/search transitions;
- HOME resolver;
- real Pair New candidate/handoff projection;
- remapper/profile summaries;
- Saved Devices pagination/status/name colors;
- Learn/First Connected instructional controls;
- Help/Lock/Back/Cancel;
- long-name policy;
- no annotations or removed screens.

### Physical closure

Full numbered screen/input matrix on real hardware, including Pair New handoff and unplug/back-to-searching Help path.

---

# mbr-09 — Resilience and regression qualification

**Depends on:** mbr-08.

**Purpose:** attack integrated failure boundaries.

### Required tests

- repeated power cycles;
- corrupt newest state -> previous generation fallback;
- no valid state -> safe first-search defaults;
- persistence interruption/fault injection where feasible;
- disconnect while each target/Escape held;
- two physical buttons mapping to one target;
- profile change while held;
- queue/parser continuity loss;
- Pair New timeout/cancel/stale-candidate races;
- old disconnect during Pair New;
- replacement handoff failure/recovery;
- remove vs disconnect race;
- bonded reconnect with absent peer;
- HID++ unsupported/failure path;
- long names/unusual/malformed Report Maps;
- sustained Mouse traffic with UI/lock;
- USB identity stability/suspend-resume where applicable.

### Physical closure

Focused resilience matrix + soak. Any stuck output, corrupted registry, unexplained connection loss, frozen UI or USB re-enumeration keeps gate open.

---

# mbr-10 — Final release qualification

**Depends on:** mbr-09.

**Purpose:** create first immutable accepted MBR baseline.

### Required work

- freeze exact source SHA/tree;
- clean production build;
- rerun host/architecture/layout tests;
- production Pico 2 W build;
- verify exact USB descriptor and forbidden-feature checks;
- release-candidate UF2 + hash/size/toolchain metadata;
- complete physical matrix covering first pair, saved reconnect/timeout, Pair New handoff/failure/help flow, every profile, Custom, HID++, Saved Devices/removal, Help/Lock/navigation, persistence, disconnect safety, fixed USB identity;
- operator reports every required scenario PASS on exact candidate;
- immutable acceptance record.

No release/tag is implied merely by preparing a candidate.

## Universal physical-gate rule

A physical gate is not complete because CI/build is green. Executor supplies exact UF2/source SHA/hash and numbered scenarios. Only operator evidence can close those scenarios. A failure keeps the same gate open and invalidates affected evidence after fixes.
