# HOPE-10 — candidate

Status: **CANDIDATE READY / PHYSICAL ACCEPTANCE PENDING**.

Date: 2026-09-23.

## Product candidate

- repository: `remappingbridge/remappingbridge`
- accepted functional base: HOPE-28 tree `95eaf0c45a0b92e50ea203e7dd9056db2036c0f9`
- current main restoration commit: `3471e983cb7ec048c9ebb4b057633ff1b028e3aa`
- branch: `hope/hope-10-remapper-options`
- candidate commit: `45407b30d8f438586ad4d380c65ce5dfb2415b64`
- draft PR awaiting physical acceptance: `#15`

## Promotion correction

PR #14 was prematurely merged during operator interaction before physical acceptance. That promotion is **not acceptance**.

The repository was immediately restored with commit `3471e983cb7ec048c9ebb4b057633ff1b028e3aa`, whose tree is exactly the accepted HOPE-28 tree. HOPE-10 was then reopened as draft PR #15 from the original candidate branch. Do not promote HOPE-10 again until the operator explicitly accepts the physical test.

## Current Mouse UI v1 authority

Synchronized against:

- `mouse-ui/src/projector/screens.c@5f269e9625ae0d02a85b5d39eb87026edc448068`
- `mouse-ui/src/navigation/navigation.c@5f269e9625ae0d02a85b5d39eb87026edc448068`

Canonical screen:

~~~text
MOUSE OPTIONS
 PASSTHROUGH
 STANDARD REMAP
 ESCAPE REMAP
 CUSTOM REMAP

JOY PRESS: ACCESS
KEY B: BACK
KEY X: HELP
~~~

## Replacement

The existing `BLU2USB_SCREEN_MOUSE_OPTIONS` slot was replaced in-place.

Removed from this flow point:

- `PAIR MOUSE`;
- `DEFAULT REMAP` wording;
- the old five-option layout.

No parallel remapper-options screen was created.

## Behavior

- exactly four selectable profile rows;
- Up/Down wraps;
- current profile is cyan;
- selected row is white and overrides cyan;
- Joy Press preserves the existing functional profile destinations:
  - Passthrough active/not-active;
  - Standard active/not-active;
  - Escape active/not-active;
  - Custom edit;
- KEY B returns through HOME resolver;
- current Mouse UI v1 Joy Left shortcut also returns HOME;
- KEY X remains inert until HOPE-29;
- global KEY Y lock remains functional;
- Pair New is no longer reachable from remapper-options and remains available from accepted home-connected.

## Visual profile correction

Because the old `PAIR MOUSE` row was removed, active-profile cyan mapping was shifted to:

- Passthrough -> row 1;
- Standard -> row 2;
- Escape -> row 3;
- Custom -> row 4.

The obsolete rule that colored row 1 cyan merely because a Mouse was connected was removed.

## Changed files

- `src/renderer/profile_feedback.c`
- `src/ux_model/ux_model.c`
- `tests/CMakeLists.txt`
- `tests/test_g06_profile_ui.c`
- `tests/test_hope10_remapper_options.c`
- `tests/test_hope10_remapper_options_source.py`

No BLE, storage, remap engine, profile persistence, USB HID or pairing implementation file changed.

## Automated verification

GitHub Actions run: `35834313013` — **SUCCESS**.

- host-architecture: **SUCCESS**
- Pico 2 W production: **SUCCESS**
- UF2 verification/upload: **SUCCESS**

Focused tests freeze:

1. exact current-v1 9-row literal;
2. four options only;
3. absence of legacy Pair Mouse and Default Remap copy;
4. active cyan rows 1..4;
5. selected white precedence;
6. active/inactive profile destinations;
7. Custom destination;
8. B and Joy Left HOME behavior;
9. X inert until HOPE-29;
10. global Y lock;
11. Pair New absent from remapper-options.

## Candidate UF2

GitHub Actions artifact:

- artifact id: `10738621023`
- ZIP size: **330,352 bytes**
- ZIP digest: `sha256:2788c13132de2bdcf741f0ad9769abe985a232842e47464e1fcc57a94ee8ce79`

Extracted firmware:

- file: `HOPE-10-remapper-options-pico2w.uf2`
- size: **896,512 bytes**
- SHA-256: `42a40d9079bdad2cf22cec939d69862c0ba1912c1bc6e4d7f54a5d7ec265fdfa`

HOPE-10 remains **not accepted** and draft PR #15 remains unmerged until physical acceptance.
