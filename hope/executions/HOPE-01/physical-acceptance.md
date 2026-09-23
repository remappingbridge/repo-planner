# HOPE-01 — physical acceptance

Status: **PENDING OPERATOR TEST**.

Candidate:

- branch: `hope/hope-01-searching-first`
- commit: `987afd16e951160860c77bd9f304dca586c339d0`
- draft PR: `#2`
- UF2: `blu2usb_picow.uf2`
- size: 880,640 bytes
- SHA-256: `2ef475f7d7fd26914cfd8e86ff2a990b03d923696155312c8281956d8826a03b`

## Required physical scenarios

1. With no previously bonded/saved Mouse, boot must show exactly `SEARCHING FIRST MOUSE`.
2. Confirm the complete 9-row text, coordinates and full dark-magenta background.
3. Confirm title magenta, the two waiting/instruction rows yellow, and resting HAT labels light-gray.
4. Press/release Joy Up, Down, Left, Right, Press and Keys A/B/X/Y; only the matching visible token(s) become white while held and return on release.
5. Confirm `KEY B` does not navigate or cancel discovery.
6. Confirm `KEY Y` does not lock or turn off the backlight.
7. Pair a valid BLE HOGP Mouse directly while this screen is visible.
8. Once the Mouse reaches ready state, confirm the inherited G06 HOME appears. This is the intentional HOPE-01 bridge; `first-mouse-connected` belongs to HOPE-02.
9. Confirm Mouse X/Y movement reaches the host.
10. Confirm Left/Right/Middle and supported wheel/Forward/Backward continue working.
11. Confirm the accepted G06 profile/remap/persistence behavior has not regressed.
12. Reboot with an already bonded Mouse and confirm it is not treated as a first unsaved Mouse; the inherited G06 startup presentation is allowed at this gate and bonded reconnect must still work.
13. Confirm no Bluetooth Keyboard or Composite pairing was introduced.

## Operator result

Operator declaration: **PENDING**.

Do not merge PR #2 and do not mark HOPE-01 ACCEPTED until the operator explicitly reports PASS.
