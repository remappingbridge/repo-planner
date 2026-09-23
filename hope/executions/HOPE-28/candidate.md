# HOPE-28 — candidate

Status: **CANDIDATE READY / PHYSICAL ACCEPTANCE PENDING**.

Date: 2026-09-23.

## Product candidate

- repository: `remappingbridge/remappingbridge`
- accepted base: `main@95b8e6fab1710f562b7ce1ead629a9cca2734c17`
- branch: `hope/hope-28-help-home-connected`
- candidate commit: **SUPERSEDED — replacement CI pending**
- draft PR: `#13`
- PR remains unmerged until operator physical acceptance.

## Canonical screen

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

## Behavior implemented

- KEY X from accepted home-connected opens Help.
- Help has zero selectable options.
- Help owns KEY Y, so Y acts only as Any Key Back and never locks.
- every complete HAT interaction returns from Help and is consumed;
- normal return restores the HOME selection that was active before Help opened;
- entry/exit emits no pairing/remap command and does not alter current profile, saved devices, current Mouse, or Pair New state.

## Disconnect/reconnect while Help is open

Mouse UI v1 behavior is preserved:

- if the current Mouse disconnects while Help is visible, the Help remains visible;
- its return target is retargeted from HOME to accepted `home-searching`;
- Any Key Back after that disconnect returns to `SEARCHING SAVED MOUSE`, never stale home-connected;
- accepted G06 reconnect/search behavior continues underneath;
- if a saved Mouse reconnects before Help is dismissed, the return target is restored to live HOME so exiting Help cannot send a connected Mouse into stale searching UI.

## Rendering

- exact 9-row literal;
- body rows 0–7 are black;
- only row 8 `ANY KEY: BACK` is dark-magenta hint region;
- title is magenta;
- explanatory body is static yellow;
- no additional Help-specific renderer exception was required because the canonical body contains no control-prefixed line before row 8.

## Changed files

- `include/blu2usb/ux_model/ux_model.h`
- `src/app/main.c`
- `src/ux_model/ux_model.c`
- `tests/CMakeLists.txt`
- `tests/test_hope03_home_connected.c`
- `tests/test_hope03_home_connected_source.py`
- `tests/test_hope28_help_home_connected.c`
- `tests/test_hope28_help_home_connected_source.py`

No BLE, remap, profile, storage, USB HID or pairing implementation file changed.

## Automated verification

Canonical Previous GitHub Actions run: `35832178259` — **SUPERSEDED by copy correction**.

### host-architecture — SUCCESS

All **31/31** tests pass, including:

- inherited G02–G06 tests;
- all accepted HOPE regressions;
- accepted HOPE-03 HOME regression evolved through the new Help;
- HOPE-28 exact literal/render test;
- HOPE-28 all-control Any-Key return semantics;
- KEY Y no-lock Help ownership;
- HOME selection preservation;
- disconnect return retargeting;
- source/runtime integration assertions;
- architecture/screen-contract tests.

The first CI attempt `35832105183` compiled successfully and had 30/31 runtime tests green; the single failure was an invalid textual assertion inside the newly written HOPE-28 source test. That assertion was removed without changing product behavior. The canonical final run above is fully green.

### pico2-w-production — SUCCESS

- pinned ARM toolchain: PASS;
- pinned Pico SDK: PASS;
- Pico 2 W configure/build: PASS;
- UF2 verification: PASS;
- artifact upload: PASS.

## Candidate UF2

GitHub Actions artifact:

- artifact id: `10738046721`
- name: `blu2usb-picow-production-pico2w`
- ZIP size: **330,388 bytes**
- ZIP digest: `sha256:ed81c1776c6eb77dd10891c503fca038f9c7d71410ca1fc4f813eb364565c19a`

Extracted firmware:

- file: `HOPE-28-help-home-connected-pico2w.uf2`
- size: **896,512 bytes**
- SHA-256: `1d8a5799ca8bf0690a1e48c42e5674b7f5435b122010b8231646fef6e6ccb9e7`

## Gate state

HOPE-28 is **not ACCEPTED** until the operator validates this exact candidate on hardware.


## Copy correction before physical acceptance

The first candidate used stale documentation copy. Current Mouse UI v1 executable source at `src/projector/screens.c@5f269e9625ae0d02a85b5d39eb87026edc448068` defines `help-home-connected` with the updated `REMOVE CONNECTED HELP` / numbered-step text.

The previous candidate `aa7c5ce7080793b83490aaf275d8cee7fb6878e6` and UF2 hash `1d8a5799...` are invalidated for physical acceptance.
