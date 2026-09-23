# HOPE-28 — pre-implementation

Status: **IN PROGRESS — IMPLEMENTATION NOT YET PHYSICALLY ACCEPTED**.

Date: 2026-09-23.

## Objective

Implement Mouse UI v1 `help-home-connected` on top of accepted HOPE-03.

Accepted product base:

- repository: `remappingbridge/remappingbridge`
- base: `main@95b8e6fab1710f562b7ce1ead629a9cca2734c17`
- branch: `hope/hope-28-help-home-connected`

## Canonical screen

~~~text
HOME CONNECTED HELP
TO DISCONNECT THE
CURRENTLY CONNECTED
MOUSE, NAVIGATE TO:
SAVED DEVICES >
(MOUSE PAGE) > REMOVE
DEVICE > REMOVE

ANY KEY: BACK
~~~

## Behavior

- KEY X from accepted home-connected opens this Help.
- Opening Help must not alter connection, profile, Pair New state, saved devices, or remap.
- Help owns KEY Y: Y acts only as Any Key Back and never locks.
- Any complete HAT interaction exits Help and is consumed.
- Normal exit returns to home-connected and restores the HOME selection that was active before opening Help.
- If the current Mouse disconnects while Help is open, the Help may remain visible but its return target becomes accepted `home-searching`; exiting Help must not resurrect home-connected for an offline Mouse.
- The accepted G06 saved reconnect lifecycle remains authoritative underneath.

## Rendering

- exact 9-row literal;
- body rows 0–7 use black background;
- only row 8 `ANY KEY: BACK` is the dark-magenta hint region;
- title magenta;
- explanatory text static yellow;
- Help has zero selectable options.

## Minimal implementation

- add one `BLU2USB_SCREEN_HELP_HOME_CONNECTED` screen state;
- add exact template;
- add to Help semantics so global Y lock is suppressed;
- KEY X from `BLU2USB_SCREEN_HOME` stores return screen + current selection, then opens Help;
- special return restores the saved selection only when returning to HOME;
- on disconnect while Help is visible, retarget return to `HOME_SEARCHING` selection 0;
- no BLE search/remap logic changes.

## Out of scope

- HOPE-10 remapper-options;
- HOPE-29 help-remapper-options;
- Saved Devices redesign;
- profile screen replacements.

## Automated verification

Tests must prove:

1. exact canonical literal;
2. hint starts only at row 8;
3. X from HOME opens Help;
4. HOME selection is preserved across Help;
5. every HAT control exits and is consumed;
6. Y never locks in Help;
7. disconnect while Help open retargets return to home-searching;
8. Help has no options;
9. no Pair New/search/remap command is emitted by Help entry/exit;
10. accepted HOPE-03 HOME behavior remains intact;
11. inherited regressions remain green.

## Physical acceptance

1. Reach home-connected.
2. Move selection to a nonzero HOME option.
3. Press/release KEY X.
4. Confirm exact Help layout.
5. Verify only `ANY KEY: BACK` has magenta hint background.
6. Press/release KEY Y; it must return without locking.
7. Confirm the same HOME selection is restored.
8. Repeat with another HAT control.
9. Open Help again, then power off/disconnect the current Mouse.
10. Exit Help; it must return to `SEARCHING SAVED MOUSE`, not stale home-connected.
11. Confirm saved reconnect proceeds normally.
12. Confirm profile/remap/persistence/Pair New behavior is unchanged.
13. Confirm no Keyboard/Composite behavior was introduced.
