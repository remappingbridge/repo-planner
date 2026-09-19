# Target architecture — Mouse Bridge Remapper

Status: **DESIGN ONLY / NO MBR GATE EXECUTED**.

This architecture is intentionally simpler than the superseded simultaneous-mouse design. The product may save many mice but allows **zero or one live connected Mouse session**.

## 1. Architectural goals

1. Mouse is the only Bluetooth product device type.
2. Multiple saved Mouse records are allowed.
3. At most one Mouse may be ready/connected at any time.
4. Pair New replaces the live session; it never overlaps two authoritative Mouse sessions.
5. HOME uses one deterministic resolver for startup, navigation and disconnect recovery.
6. BLE transport details never leak into remap, UI or USB logic.
7. Held output is released safely before disconnect, replacement, removal or invalidation.
8. Product state and Bluetooth security credentials have separate ownership and coordinated deletion.
9. UI is event/projector driven and never calls BTstack primitives directly.
10. Accepted G06 reconnect, persistence, Logitech HID++, release-safety, pixel-layout and interaction lessons become regression constraints from the first relevant gate.
11. Bluetooth Keyboard/Composite product modules do not exist.
12. Synthetic Escape is allowed only as minimal USB Keyboard output from Mouse remapping.

## 2. System diagram

```text
HAT GPIO
   |
   v
+------------------+       +--------------------+
| hat_input        |------>| interaction_engine |
+------------------+       | release-triggered  |
                           +----------+---------+
                                      |
                                      v
                           +--------------------+
                           | application_service|
                           +--+-------+-------+--+
                              |       |       |
                   +----------+       |       +----------------+
                   v                  v                        v
          +----------------+   +-------------+       +------------------+
          | ui_projector   |   | profiles /  |       | pairing          |
          | screen models  |   | remap       |       | coordinator      |
          +-------+--------+   +------+------+       +---------+--------+
                  |                   |                        |
                  v                   v                        v
          +----------------+   +-------------+       +------------------+
          | renderer       |   | output_state|<------| mouse_session    |
          | ST7789 240x240 |   | one session |       | None | one ready|
          +----------------+   +------+------+       +---------+--------+
                                      |                        |
                                      v                        v
                               +-------------+       +------------------+
                               | usb_hid     |       | ble_hogp        |
                               | Mouse +     |       | one live client |
                               | Escape sink |       +---------+--------+
                               +-------------+                 |
                                                               v
                                                      +------------------+
                                                      | bt_runtime       |
                                                      | one CYW43 owner  |
                                                      +------------------+

application_service -> mouse_registry -> product_storage
ble_hogp -> logitech_hidpp for the current session only
bt_runtime -> BTstack credential storage
```

`app/main` may compose modules and pump bounded events. It may not become a second owner of BTstack, TinyUSB, GPIO/SPI or flash serialization.

## 3. Domain model

### 3.1 Persistent identity

```text
MouseId
  persistent identity used by Saved Devices / product storage

MouseSessionId
  MouseId + generation/token for one live or candidate session
```

Raw HCI/HIDS handles remain adapter-private.

The generation/token prevents a stale callback from a disconnected/replaced session from mutating a newer session that reused an underlying handle.

### 3.2 Saved Mouse

```text
SavedMouse {
  MouseId id;
  DisplayName name;
  ProfileKind profile_kind;
  CapabilitySummary capabilities;
  VendorMetadata vendor_metadata;
}
```

There may be several `SavedMouse` records.

There is no Keyboard/Composite device-type union in the product domain.

### 3.3 Live session

```text
ConnectedMouseSlot = None | MouseSession

MouseSession {
  MouseSessionId session_id;
  MouseId mouse_id;
  ConnectionPhase phase;
  ReportCapabilities reports;
  bool secured;
  bool ready;
  HidppSessionState vendor;
}
```

Invariant:

```text
ready_live_mouse_count <= 1
```

## 4. Canonical Mouse event boundary

BLE adapters emit canonical events only:

```text
ButtonDown(LEFT|RIGHT|MIDDLE|FORWARD|BACKWARD)
ButtonUp(LEFT|RIGHT|MIDDLE|FORWARD|BACKWARD)
Move(dx, dy)
Wheel(vertical)
Pan(horizontal)
SessionGone(session_id)
```

Rules:

- no Report ID or HID field offset survives this boundary;
- malformed/truncated frames are rejected before canonical emission;
- duplicate Down/Up is idempotent;
- session cleanup is mandatory on disconnect, replacement, parser reset, fatal queue continuity loss and removal.

## 5. Output-state model

The superseded cross-mouse aggregator is removed.

The current session still requires explicit held-state safety because two physical source buttons may map to the same output target.

Conceptually:

```text
owners[LEFT]     = set of current-session physical/remapped sources
owners[RIGHT]    = ...
owners[MIDDLE]   = ...
owners[FORWARD]  = ...
owners[BACKWARD] = ...
owners[ESCAPE]   = ...
```

The set never contains sources from two different live mice because only one Mouse session may be authoritative.

Consequences:

- releasing one physical source does not release a target still owned by another physical source from the same mouse;
- disconnect/replacement/removal releases every owner from the outgoing session;
- profile change releases stale owners from the previous mapping;
- relative X/Y/wheel/pan remain transient and use bounded accumulation/backpressure handling.

## 6. Bluetooth runtime

### 6.1 Owner

There is exactly one CYW43/BTstack lifecycle owner.

Use the accepted G06 BLE-only runtime as the starting evidence. Do not import G07 Classic-Keyboard multicore architecture merely because it exists historically.

### 6.2 Session model

Only one HOGP session may reach ready state.

The implementation may temporarily have a discovery/connection candidate while replacing another session only if that candidate cannot become authoritative before the old session has completed release/disconnect cleanup. The preferred simple sequencing is disconnect-first, then discover/connect replacement.

Session state includes:

- connection/security state;
- HIDS client context;
- Report Map and parsed Mouse fields;
- notification/report subscriptions;
- identity correlation;
- timers/retries;
- optional HID++ correlation state.

## 7. Pairing/search coordinator

Search is modeled as explicit asynchronous transactions.

### 7.1 First Mouse

```text
registry empty
 -> start finite FIRST_MOUSE cycle
 -> first valid unsaved candidate
 -> authenticate/classify/persist
 -> ready live session
 -> stop search

cycle timeout/no candidate
 -> start another cycle
```

Logical first-Mouse search is therefore continuous while no Mouse is saved.

### 7.2 Unified HOME resolver

```text
resolve_home():
  if saved_mice.empty():
    show searching-first
    ensure FIRST_MOUSE search active
  else if live_mouse.ready():
    show home-connected
    cancel/avoid saved search
  else:
    show home-searching
    start bounded SEARCH_SAVED transaction
```

This resolver is used for:

- startup;
- returning to HOME;
- unlocking when HOME is the destination;
- current Mouse disconnect/power-off;
- returning from failed/canceled Pair New with no live session.

`SEARCH_SAVED` accepts the first saved Mouse that reaches ready state and stops. If the bounded window expires, project `home-retry` / `DEVICE NOT FOUND`.

### 7.3 Pair New replacement

```text
request PAIR_NEW
 -> cancel incompatible search
 -> if live_mouse exists:
      stop new events from it
      release held Mouse/Escape state
      disconnect it
      clear live slot
      keep SavedMouse + bond
 -> search only for unsaved valid Mouse
 -> first accepted candidate persists + becomes sole live Mouse
 -> stop Pair New
```

If Pair New times out/cancels, the old Mouse remains saved but disconnected. Pair New does not silently reconnect it.

## 8. Registry and persistence

### 8.1 Ownership domains

- **BT credentials:** BTstack-owned security material.
- **Product state:** saved names, profile kinds, Custom state and capability/vendor metadata.

They may share physical flash infrastructure but must remain logically/physically protected from overwriting each other.

### 8.2 Product record

Versioned record should include:

```text
header { schema_version, generation, length, checksum }
saved_mice[]
global_custom_template
custom_draft
custom_draft_dirty
```

Do not persist transient live handles, HIDS pointers or queue state.

### 8.3 Power-loss behavior

Use alternating generations/slots or a proven equivalent. Write and verify a new generation before considering it current. Boot selects newest valid generation and falls back to the previous valid one if necessary.

### 8.4 Remove transaction

For the target `MouseId`:

1. if it is live, stop new events;
2. release held output;
3. disconnect/clear live session;
4. remove product registry/profile association;
5. remove relevant BT credentials;
6. persist verified product generation;
7. publish completion.

Removing a disconnected saved Mouse does not disturb the current live Mouse.

## 9. Profiles/remap

`profiles` and `remap` remain host-pure.

`ProfileKind = PASSTHROUGH | DEFAULT_OR_STANDARD | ESCAPE | CUSTOM`.

The connected Mouse is the only runtime profile target. Each saved Mouse still stores its own confirmed profile kind for restoration when it later becomes connected.

`CustomTemplate` remains global unless a later explicit product change supersedes it.

Apply transaction:

```text
request apply
 -> validate profile
 -> release stale held output from old mapping
 -> apply runtime/vendor requirements
 -> persist verified profile state
 -> publish confirmed profile changed
 -> UI may show ACTIVE/APPLIED state
```

No optimistic success.

## 10. Escape and USB

Escape is retained as a deliberate output-only exception.

`usb_hid` is the only TinyUSB owner and exposes:

- fixed Mouse HID output;
- minimal Keyboard HID output sufficient for synthetic Escape;
- no diagnostic CDC or other debug interface.

Bluetooth never exposes a Keyboard role.

USB identity is fixed from boot and must not re-enumerate because of Bluetooth/profile/UI state.

Exact final VID/PID/strings remain an mbr-00 decision.

## 11. Logitech HID++

HID++ is bound only to the current Mouse session.

- capability probe is session-scoped;
- preserve accepted `REPROG_CONTROLS_V4` Forward behavior where needed;
- preserve true down/hold/up;
- unsupported peers fail safe to Standard HOGP;
- profile transition/disconnect/replacement clears stale vendor-derived held output.

No concurrent HID++ contexts are required.

## 12. UI architecture

`interaction_engine` receives HAT transitions and emits semantic commands. `ui_projector` derives screen models. `renderer` only draws them.

Key projection rules:

- `home-connected` always shows the one connected Mouse name/profile;
- `home-searching` automatically corresponds to an active bounded saved search;
- current Mouse disconnect causes immediate projection to `home-searching` when saved records remain;
- saved-search timeout projects `home-retry`;
- Saved Devices has one saved Mouse per page and at most one cyan/CONNECTED page;
- there is no multi-connected count/focus state.

Actions remain release-triggered; Help/lock/color/pixel rules follow accepted G06 lessons and current canonical screen documentation.

## 13. Concurrency and bounded queues

Bluetooth callbacks, application/UI and USB service communicate through bounded structures with explicit overflow behavior.

Requirements:

- no unbounded allocation in report callbacks;
- overflow invalidating state continuity triggers release-safe reset of the current session;
- flash writes cannot block critical USB/radio servicing indefinitely;
- repeated Mouse reports do not force unnecessary full-screen redraws;
- stale transaction/session events are ignored by ID/generation.

## 14. Architecture guards

Automated checks should reject:

- `.c` textual includes;
- raw BTstack outside Bluetooth runtime/adapters;
- TinyUSB ownership outside `usb_hid`;
- GPIO/SPI outside HAT/renderer platform adapters;
- flash erase/program outside storage infrastructure;
- Bluetooth Keyboard/Composite production modules;
- diagnostic CDC product descriptors;
- second CYW43/BTstack lifecycle owner;
- UI calling transport functions;
- transport-specific report structs in remap/USB/UI;
- more than one ready Mouse session;
- simultaneous-Mouse aggregation/session-manager code introduced as speculative future-proofing.

## 15. Planned module boundaries

Names may be refined before mbr-01, but conceptual ownership is:

- `domain`
- `mouse_registry`
- `mouse_session`
- `output_state`
- `profiles`
- `remap`
- `pairing_coordinator`
- `bt_runtime`
- `ble_hogp`
- `logitech_hidpp`
- `product_storage`
- `usb_hid`
- `interaction`
- `ui_projector`
- `renderer`
- `hat`
- `app`

There is intentionally no `classic_hid`, `keyboard_transport`, Composite module, multi-session Mouse manager or cross-Mouse aggregator.

## 16. Architecture acceptance principle

The architecture is successful when it makes invalid product states difficult or impossible to express:

- two ready mice cannot coexist;
- stale callbacks cannot mutate a replacement session;
- Pair New cannot delete the old saved record merely because it disconnects it;
- a disconnect cannot leave held Mouse/Escape output stuck;
- HOME cannot remain falsely connected after session loss;
- search timeout cannot run forever under saved-search policy;
- UI cannot bypass pairing/removal/persistence orchestration;
- Escape cannot grow into Bluetooth Keyboard support.
