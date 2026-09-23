# HOPE-01 — pre-implementation

Status: **IN PROGRESS — IMPLEMENTATION NOT YET PHYSICALLY ACCEPTED**.

Date: 2026-09-23.

## Objective

Implement only the Mouse UI Layout 1.0 screen `searching-first` on top of the accepted HOPE-00/G06 product, preserving the existing BLU2USB pairing/input architecture.

## Dependency

- HOPE-00: **ACCEPTED**
- accepted destination base: `remappingbridge/remappingbridge@16b9a3a2f164f08e3a9c25fc39a4767b04f48f39`
- planned branch: `hope/hope-01-searching-first`

## Pinned provenance

- destination base: `16b9a3a2f164f08e3a9c25fc39a4767b04f48f39`
- BLU2USB G06: `7eee024ad4ee726c5a85ffa2f32b9f47187878af`
- Mouse UI Layout 1.0: `e8adad7919e931c92515bf655ef4050876a8e7a9`

Mouse UI documents consulted for this gate:

- `docs/product/ui-layout-v1.0.md`
- `docs/spec/01-screen-reference.md`
- `docs/spec/03-first-start-and-pairing.md`
- `src/projector/screens.c`
- `src/projector/projector.c`
- `src/renderer/renderer.c`
- `tests/goldens/mui-04/screens.txt`

## Old screen / new screen

Old inherited first-start presentation:

- `BLU2USB_SCREEN_LEARN_KEYS`
- title `PRESS TO LEARN A KEY`
- full dark-magenta didactic background
- existing automatic BLE Mouse discovery already runs independently of that screen.

New screen introduced by this gate:

~~~text
SEARCHING FIRST MOUSE
PRESS TO LEARN KEYS
WHILE WAIT CONNECTION
       JOY UP
  JOY    JOY    JOY
  LEFT  PRESS  RIGHT
      JOY DOWN
 KEY A         KEY X
 KEY B         KEY Y
~~~

## Observable rules to implement

- use the exact 9 semantic rows above;
- 21-column layout;
- full-screen dark-magenta background;
- title uses the existing title tone;
- `PRESS TO LEARN KEYS` and `WHILE WAIT CONNECTION` use the system body/yellow tone;
- resting HAT labels use action/light-gray;
- while a displayed HAT control is held, only that control's visible token(s) become white;
- release restores the resting tone;
- joystick, A, B, X and Y are all didactic on this screen;
- `KEY B` does not navigate;
- `KEY Y` does not lock;
- no HAT control cancels discovery.

Frozen 0-based columns used by the existing renderer implementation:

- `JOY UP`: 7;
- top three `JOY`: 2 / 9 / 16;
- `LEFT` / `PRESS` / `RIGHT`: 2 / 8 / 15;
- `JOY DOWN`: 6;
- `KEY A` / `KEY X`: 1 / 15;
- `KEY B` / `KEY Y`: 1 / 15.

The G06 didactic renderer already uses title Y=8, body Y=39 and 25-pixel didactic advance, matching Mouse UI v1. This gate reuses those renderer primitives rather than importing mouse-ui architecture.

## Saved-device condition and minimal adaptation

G06 does not contain the later Mouse UI saved-device registry. It does already own the persistent BTstack LE bond database, and Bluetooth Keyboard/Composite pairing does not exist in G06.

For HOPE-01 only, “saved Mouse exists” is therefore mapped minimally to “the existing G06 persistent LE device database contains a bonded peer”. This avoids creating the future Saved Devices architecture early.

Behavior at startup:

- no persisted G06 Mouse bond -> show `searching-first`;
- persisted G06 Mouse bond -> preserve the old G06 first-screen behavior for now, until the later HOME/search gates replace it.

## Temporary post-success bridge

Mouse UI v1 requires `first-mouse-connected` after first success, but that screen belongs to **HOPE-02** and must not be anticipated.

For this gate, when a fully ready BLE HOGP Mouse connects while `searching-first` is visible, the product transitions to the existing inherited G06 `HOME`. HOPE-02 will replace this temporary inherited successor with the canonical `first-mouse-connected` feedback.

The BLE `CONNECTED` event already means the HIDS descriptor has been accepted, a Mouse parser exists and the BLE state is READY; no earlier advertisement/connection event is used.

## Files expected to change

- `include/blu2usb/ux_model/ux_model.h`
- `src/ux_model/ux_model.c`
- `src/renderer/renderer.c`
- `include/blu2usb/ble_hogp/ble_hogp.h`
- `src/ble_hogp/ble_hogp_pico.c`
- `src/app/main.c`
- `tests/CMakeLists.txt`
- one focused HOPE-01 host test.

## Reused

- G06 BLE HOGP discovery, security, HIDS qualification and bonded reconnect;
- G06 HAT input/press tracking;
- G06 ST7789 renderer, glyphs, palette and didactic Y geometry;
- G06 USB HID Mouse/Keyboard identity and Mouse forwarding/remapping;
- G06 persistent BTstack bond database.

## Explicit removals

None.

The old `LEARN THE KEYS` screen remains in the product because its canonical replacement is HOPE-23, not HOPE-01.

## Out of scope

- `first-mouse-connected` (HOPE-02);
- new HOME states (HOPE-08/09/03);
- Pair New;
- Saved Devices registry;
- removal flow;
- changing profiles/remap;
- UI↔Core contracts;
- mouse-core architecture;
- Bluetooth Keyboard/Composite pairing;
- architectural refactor.

## Automated verification

Run the inherited host suite and focused HOPE-01 tests, then the unchanged Pico 2 W production build.

The focused tests must prove:

1. exact 9-row `searching-first` text;
2. full didactic dark-magenta background;
3. prompt rows use system yellow/body tone;
4. HAT resting labels are light gray;
5. each HAT press produces white didactic feedback at the frozen coordinates;
6. release returns to normal;
7. B is inert;
8. Y is inert and does not lock;
9. no control leaves `searching-first`.

## Physical scenarios

1. boot with no saved/bonded Mouse -> exact `SEARCHING FIRST MOUSE` screen;
2. verify full dark-magenta background and exact text/geometry;
3. verify prompt text is yellow;
4. press/release each joystick direction, joystick press, A/B/X/Y and verify only the corresponding label becomes white while held;
5. verify B does not navigate;
6. verify Y does not turn off the backlight/lock;
7. pair a BLE HOGP Mouse from this screen;
8. after the Mouse becomes ready, verify the temporary inherited G06 HOME appears;
9. verify X/Y movement;
10. verify Left/Right/Middle and supported wheel/Forward/Backward;
11. verify previously accepted G06 remap/persistence behavior is not regressed;
12. reboot with an already bonded Mouse and verify the product does not incorrectly treat that bond as a first unsaved Mouse;
13. verify no Bluetooth Keyboard/Composite pairing was added.

## Risks

- querying the persistent LE DB before BTstack initialization;
- a connection-ready event arriving while the first screen is being initialized;
- accidental change to the old Learn screen geometry/interaction;
- treating press feedback as navigation;
- introducing future HOPE screens early;
- pairing/input regression from changing startup ordering.

The implementation must keep the adaptation minimal and preserve the accepted G06 runtime.
