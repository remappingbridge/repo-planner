# HOPE-28 — physical acceptance

Status: **PENDING OPERATOR TEST**.

Candidate:

- branch: `hope/hope-28-help-home-connected`
- commit: **SUPERSEDED — replacement CI pending**
- draft PR: `#13`
- UF2: **SUPERSEDED — replacement pending**
- size: **896,512 bytes**
- SHA-256: **SUPERSEDED**

## Required physical scenarios

1. Reach accepted home-connected with a live saved Mouse.
2. Move the HOME selection to a nonzero row.
3. Press/release KEY X.
4. Exact Help must appear:

~~~text
REMOVE CONNECTED HELP
TO DISCONNECT THE
CURRENTLY CONNECTED
MOUSE NAVIGATE TO:
STEP 1. SAVED DEVICES
STEP 2. REMOVE DEVICE
STEP 3. KEY A: REMOVE

ANY KEY: BACK
~~~

5. Verify rows 0–7 use black background and only `ANY KEY: BACK` uses dark-magenta hint background.
6. Press/release KEY Y while Help is visible. It must return from Help and must **not** lock or switch off backlight.
7. Confirm the same HOME selection from step 2 is restored.
8. Re-enter Help and exit with another HAT control; the interaction must be consumed and must not activate the selected HOME option.
9. Confirm no Pair New, remap apply, profile change, removal, or saved-device mutation occurs from Help entry/exit.
10. Open Help again and power off/disconnect the current Mouse.
11. Help may remain visible after disconnect.
12. Exit Help. It must return to accepted `SEARCHING SAVED MOUSE`, not stale home-connected.
13. Confirm accepted 8-second saved reconnect/search behavior continues.
14. If the Mouse reconnects while Help is still visible, dismiss Help and confirm return is live home-connected, not stale searching.
15. Confirm HOME name/profile summary remain correct after reconnect.
16. Confirm Mouse movement/buttons, profiles/remap, Escape output, Pair New and persistence remain functional.
17. Confirm no Bluetooth Keyboard/Composite behavior was introduced.

## Operator result

Operator declaration: **PENDING**.

Do not merge PR #13 and do not mark HOPE-28 ACCEPTED until the operator explicitly reports PASS for this exact candidate.


## Copy-correction supersession

Do not use the previous HOPE-28 UF2. Its behavior was valid, but the visible Help copy was stale relative to current Mouse UI v1 source.

Physical acceptance must use the replacement candidate generated after synchronizing with `mouse-ui/src/projector/screens.c@5f269e9625ae0d02a85b5d39eb87026edc448068`.
