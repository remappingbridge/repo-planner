# Target architecture — Mouse Bridge Remapper

Status: **DESIGN ONLY**. This is a from-zero architecture informed by accepted G06 behavior; it is not a declaration that G06 source can be copied unchanged.

## 1. Architectural goals

1. Mouse is the only Bluetooth product device type.
2. Several mice may be connected simultaneously.
3. One fixed USB Mouse-facing identity exists from boot and never follows Bluetooth connection state.
4. BLE transport details never leak into remap, UI or USB logic.
5. Every connected Mouse has independent source ownership so one Mouse cannot release another Mouse's held output.
6. Product state and Bluetooth security credentials have separate ownership and coordinated deletion.
7. UI is event/projector driven; it does not call BTstack primitives.
8. Accepted G06 reconnect, persistence, Logitech HID++, release-safety, pixel-layout and interaction lessons become regression constraints from the first relevant gate.
9. Keyboard/Composite modules do not exist in the target production architecture unless `AMB-001` is explicitly resolved by changing scope.
10. Runtime must remain responsive while scanning, reconnecting, moving several mice, rendering, locking/unlocking or writing persistent state.

## 2. System diagram

```text
                         +----------------------+
 HAT GPIO -------------->| hat_input            |
                         +----------+-----------+
                                    |
                                    v
                         +----------------------+
                         | interaction_engine   |
                         | release-triggered    |
                         +----------+-----------+
                                    | AppCommand
                                    v
+----------------+       +----------------------+       +------------------+
| ui_projector   |<------| application_service  |------>| pairing_coordinator|
| screen models  |       | orchestration only   |       | reconnect/pair    |
+-------+--------+       +---+-------+-------+--+       +--------+---------+
        |                    |       |       |                    |
        v                    |       |       |                    v
+----------------+           |       |       |           +------------------+
| renderer       |           |       |       +---------->| mouse_registry   |
| ST7789 240x240 |           |       |                   | saved/profile    |
+----------------+           |       |                   +--------+---------+
                             |       |                            |
                             |       |                            v
                             |       |                   +------------------+
                             |       +------------------>| product_storage  |
                             |                           | version/CRC/slots|
                             |                           +------------------+
                             |
                             v
                    +-------------------+
                    | profile_service   |
                    | per-Mouse kind +  |
                    | global custom     |
                    +---------+---------+
                              |
                              v
                    +-------------------+
                    | remap_engine      |
                    | canonical only    |
                    +---------+---------+
                              |
                              v
+------------------+   +-------------------+    +------------------+
| ble_hogp_adapter |-->| mouse_aggregator  |--->| usb_mouse_owner  |
| N live sessions |   | source ownership  |    | TinyUSB only     |
+---------+--------+   | + relative deltas |    +------------------+
          |            +-------------------+
          |
          +--------------------+
          |                    |
          v                    v
+------------------+   +--------------------+
| bt_runtime       |   | logitech_hidpp     |
| one CYW43/BTstack|   | per-session vendor |
| lifecycle owner  |   | capability adapter |
+---------+--------+   +--------------------+
          |
          v
+------------------+
| bt_credentials   |
| BTstack-owned    |
+------------------+
```

`app/main` may compose these modules and pump bounded events. It may not become a second owner of BTstack, TinyUSB, GPIO/SPI or flash serialization.

## 3. Domain model

### 3.1 Stable identity

```text
MouseId
  persistent identity used by product registry
  != transient connection handle

MouseSessionId
  MouseId + monotonically changing generation/session token

MouseSourceId
  MouseSessionId + logical source stream
  examples: standard_hogp, hidpp_forward
```

A Bluetooth ACL/HIDS handle is adapter-private. Domain/UI code receives `MouseId`/`MouseSessionId`, never raw BTstack handles.

The generation token prevents late callbacks from a disconnected old session from mutating a newly connected session that reused an underlying handle.

### 3.2 Saved Mouse

```text
SavedMouse {
  MouseId id;
  DisplayName name;
  ProfileKind profile_kind;
  CapabilitySummary capabilities;
  VendorMetadata vendor_metadata;   // minimal, versioned
}
```

There is no `DeviceType` union containing Keyboard/Composite in the product domain.

### 3.3 Live session

```text
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

The connection set owns zero or more sessions. “Connected” shown to the UI means a current ready runtime session, not “a report moved recently”.

### 3.4 Profiles

```text
ProfileKind = PASSTHROUGH | DEFAULT_OR_STANDARD | ESCAPE | CUSTOM
```

The exact public name `DEFAULT` vs `STANDARD` remains `AMB-006`. `ESCAPE` cannot be materialized by the planned Mouse-only USB adapter until `AMB-001` is resolved.

`CustomTemplate` remains global by inherited G06 default. Each saved Mouse stores only its selected `ProfileKind`. The multi-Mouse consequences are tracked in `AMB-023`.

## 4. Canonical Mouse event boundary

Transport adapters emit only canonical events such as:

```text
ButtonDown(source, LEFT|RIGHT|MIDDLE|FORWARD|BACKWARD)
ButtonUp(source, LEFT|RIGHT|MIDDLE|FORWARD|BACKWARD)
Move(source, dx, dy)
Wheel(source, vertical)
Pan(source, horizontal)
SourceGone(source/session)
```

Rules:

- no Report ID or HID field offset survives this boundary;
- malformed/truncated frames are rejected before canonical emission;
- duplicate Down/Up is idempotent;
- `SourceGone` is mandatory on disconnect, parser reset, fatal queue loss, device removal and relevant profile transition cleanup.

## 5. Multi-Mouse output aggregation

### 5.1 Held buttons

Use ownership sets/refcounts, conceptually:

```text
owners[LEFT]     = { MouseA/session7, MouseB/session2 }
owners[RIGHT]    = { ... }
owners[MIDDLE]   = { ... }
owners[FORWARD]  = { ... }
owners[BACKWARD] = { ... }
```

The output bit is down while the set is non-empty.

Consequences:

- Mouse A release does not cancel Mouse B hold;
- disconnecting/removing Mouse A erases only A's ownership;
- profile changes erase only old mapping ownership attributable to that Mouse/session;
- duplicate transitions are safe.

### 5.2 Relative movement/wheel/pan

Movement is not owned/held. Accumulate bounded signed totals from all ready sessions. Emit chunks that fit the fixed USB report range. Consume only the chunk actually accepted by TinyUSB. Queue/USB backpressure must not silently discard held-state transitions.

### 5.3 Ordering

Within one session, preserve adapter event order. Across sessions, the system needs deterministic queue ordering but no false claim of physical simultaneity. Button state is source-aware, so benign interleaving cannot create a cross-device release bug.

## 6. BLE/BTstack runtime

### 6.1 Owner

There is exactly one CYW43/BTstack lifecycle owner. Because the target no longer includes Classic Keyboard, the preferred starting topology is the physically accepted G06 BLE-only runtime approach rather than importing the later dual-mode/Core1 G07 architecture.

At gate execution time, the exact G06 runtime and current Pico SDK APIs must be re-inspected. A topology change is allowed only with evidence and predecessor regression tests.

### 6.2 Per-session HOGP state

The BLE adapter must be redesigned from any single-client assumptions into explicit per-connection/session contexts:

- connection/security state;
- HIDS client instance/context;
- Report Map and parsed Mouse fields;
- notification/report subscriptions;
- address/identity correlation;
- timers/retry state;
- optional HID++ correlation state.

Global mutable parser state tied to “the mouse” is prohibited.

### 6.3 Candidate classification

Discovery produces candidates. The coordinator decides whether the current operation accepts them:

- `FIRST_MOUSE`: no saved mice exist; candidate must be a valid Mouse;
- `SEARCH_SAVED`: candidate/connection identity must correspond to saved policy;
- `PAIR_NEW`: candidate must be a valid Mouse and not already saved;
- reconnect: candidate/connection corresponds to a known bond/MouseId.

The adapter does not navigate screens.

## 7. Pairing/reconnect coordinator

Model search as explicit asynchronous transactions, not blocking loops.

### 7.1 No saved Mouse

```text
BOOT
  -> FIRST_SEARCH_CYCLE
  -> candidate -> authenticate -> classify -> persist -> connect
  -> FIRST_CONNECTED_FEEDBACK

on cycle timeout/no candidate:
  restart another cycle while still logically searching indefinitely
```

“Indefinite” therefore means an application policy that restarts finite BLE operations. It does not require one unbounded BTstack call.

### 7.2 Saved Mouse boot/search

```text
BOOT_WITH_SAVED
  -> SAVED_SEARCH_ACTIVE
  -> one/more saved reconnect attempts according to AMB-004 decision
  -> if qualifying connection found: CONNECTED projection
  -> if bounded window ends with none: HOME_RETRY
```

### 7.3 Pair New

```text
PAIR_NEW_ACTIVE
  -> discover valid Mouse
  -> reject/ignore already-saved identity according to AMB-010 decision
  -> authenticate/classify/persist atomically
  -> ready session + UI feedback
  -> bounded no-new-device -> RETRY_PAIR_NEW
```

Starting a Pair New transaction never deletes a currently saved/connected Mouse.

## 8. Registry and persistence

### 8.1 Two ownership domains

- **BT credentials:** BTstack-owned security material (IRK/LTK/bond database).
- **Product state:** application-owned names, profile kinds, custom state, capability/vendor metadata and registry references.

They may share physical flash infrastructure but must have disjoint logical/physical ownership.

### 8.2 Product record

Versioned record should include:

```text
header { schema_version, generation, length, checksum }
saved_mice[]
global_custom_template
custom_draft
custom_draft_dirty
optional focused/presentation state only if explicitly approved
```

Do not persist transient connection handles, HIDS client pointers or BTstack internals.

### 8.3 Power-loss behavior

Use alternating slots/generations (or a proven equivalent). Write new generation completely, verify it, then consider it current. On boot choose newest valid generation; if corrupt/torn, fall back to prior valid generation. No valid record -> safe product defaults.

### 8.4 Remove transaction

Conceptual sequence:

1. identify `MouseId`;
2. stop new events for that session/device;
3. release all canonical/remapped ownership for that Mouse;
4. disconnect any live session;
5. remove product registry/profile association;
6. remove relevant BT credential entry;
7. persist verified new product generation;
8. publish completion to UI.

Failure handling/rollback must be specified in the gate so the product cannot claim removal while credentials/profile data remain inconsistently half-deleted.

## 9. Profile/remap engine

### 9.1 Pure core

`profiles` and `remap` are host-testable and have no Pico SDK, BTstack, TinyUSB, GPIO or renderer dependencies.

Input:

- MouseId/profile kind;
- canonical source event;
- global CustomTemplate.

Output:

- canonical Mouse target transitions/motion;
- possibly an Escape intent only if the product decision keeps Escape. The planned `usb_mouse_owner` must not silently translate Escape into a nonstandard Mouse usage.

### 9.2 Apply transaction

A new profile is not visually “active” at button release alone.

```text
request apply
 -> validate profile
 -> release stale old mapping ownership for this Mouse
 -> apply runtime mapping/vendor requirements
 -> persist verified profile state
 -> publish confirmed profile changed
 -> UI may show ACTIVE/APPLIED state
```

If persistence/runtime fails, UI must not display a false success state.

## 10. Logitech HID++ adapter

- per-session capability probe;
- no UI/USB dependency;
- only activates when a supported Mouse/profile needs Forward diversion;
- preserve true down/hold/up semantics;
- correlate responses with the correct Mouse session/generation;
- unsupported write/feature discovery fails back to Standard HOGP;
- profile transition/disconnect clears stale vendor-derived ownership.

## 11. USB owner

`usb_mouse_owner` is the only module that owns TinyUSB device descriptors, device task/report submission and host-visible Mouse report construction.

Planned invariants:

- fixed descriptor from boot;
- no Bluetooth-driven `tud_disconnect()/tud_connect()` path;
- no diagnostic USB interfaces;
- report generation uses aggregated canonical state only;
- relative deltas are consumed only after accepted report submission;
- no transport handle/report layout is visible here.

Exact VID/PID/strings and Escape-related interface decision are blocked by `AMB-001`/`AMB-018`.

## 12. UI architecture

### 12.1 Separation

`interaction_engine` receives physical HAT transitions and emits semantic commands. `ui_projector` derives screen models from application state. `renderer` only draws those models.

UI cannot call:

- raw BTstack/HIDS functions;
- TinyUSB descriptor/report primitives;
- flash erase/program directly;
- Logitech HID++ packets directly.

### 12.2 Release-triggered controls

Maintain physical state per HAT control:

```text
UP -> visual held feedback only
DOWN/RELEASE -> execute the screen's semantic action
```

Debounce belongs in HAT/input adaptation; navigation policy belongs in interaction.

### 12.3 Navigation

Represent screens as explicit states and declared transitions, not ad-hoc `if current_screen` chains. Every final screen contract must declare:

- visible rows;
- dynamic fields;
- colors;
- selectable row set/order;
- visible hints;
- hidden controls, if any;
- press feedback;
- release action;
- success/failure async transitions;
- Back parent/target;
- lock behavior;
- focus Mouse requirement.

This is essential because the supplied rules contain several incomplete/contradictory transitions.

### 12.4 Renderer

Keep semantic row/token data separate from physical pixels. A layout table converts semantic positions to the accepted 240x240 geometry. New explicit token columns are assertions over the final projected text.

Tests must verify:

- <= 21 characters per row unless a new deliberate rule changes the grid;
- exact literal text after ambiguity normalization;
- exact token start columns where specified;
- retained G03 physical vertical relocation unless explicitly superseded;
- hint-region boundary;
- color precedence;
- no overlap/clipping for dynamic names under the final truncation policy.

## 13. Concurrency and bounded queues

Bluetooth callbacks, application/UI and USB service must communicate through bounded structures with explicit overflow behavior.

Requirements:

- no unbounded allocation in report callbacks;
- overflow must trigger a source/session resynchronization or release-safe reset rather than leave held buttons;
- queues carry canonical snapshots/events with session identity;
- flash writes cannot block critical USB servicing indefinitely;
- renderer work is bounded/coalesced; repeated Mouse reports do not force a full-screen redraw.

## 14. Architecture guards

Automated static/host checks should reject:

- `.c` textual includes;
- raw BTstack includes outside Bluetooth adapters/runtime;
- TinyUSB device ownership outside `usb_mouse_owner`;
- GPIO/SPI primitives outside HAT/renderer platform adapters;
- flash erase/program outside storage infrastructure;
- Keyboard/Composite production modules while Mouse-only scope is active;
- CDC/debug product descriptors;
- a second CYW43/BTstack lifecycle owner;
- UI calling transport functions;
- transport-specific report structs in remap/USB/UI;
- single global “active mouse” used as the only runtime session state.

## 15. Planned module boundaries

Names can be refined before `mbr-01`, but ownership is frozen conceptually:

- `domain`
- `mouse_registry`
- `mouse_sessions`
- `mouse_aggregator`
- `profiles`
- `remap`
- `pairing_coordinator`
- `bt_runtime`
- `ble_hogp`
- `logitech_hidpp`
- `product_storage`
- `usb_mouse`
- `interaction`
- `ui_projector`
- `renderer`
- `hat`
- `app`

There is intentionally no planned `classic_hid`, `keyboard_transport` or `composite` production module.

## 16. Architecture acceptance principle

The architecture is successful only when it makes the dangerous states difficult or impossible to express:

- a Mouse cannot release another Mouse's held button;
- a stale callback cannot mutate a new session;
- UI cannot bypass pairing/removal transactions;
- a profile cannot claim success before persistence/runtime confirmation;
- Bluetooth state cannot change USB identity;
- vendor quirks cannot leak into generic input;
- power loss cannot make the newest partial write the only product record;
- adding a second Mouse does not require cloning global singleton state.
