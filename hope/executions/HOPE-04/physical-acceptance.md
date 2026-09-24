# HOPE-04 — saved-devices physical acceptance

Status: **PENDING OPERATOR TEST / NOT ACCEPTED**.

Candidate:

- branch: `hope/hope-04-saved-devices`
- commit: `143c00b053e22345768054786a3f334d995bbb60`
- draft PR: `#16`
- accepted base: `5c62a98a4495570237d7b4b223a090bea1020990`
- UF2: `HOPE-04-saved-devices-143c00b-pico2w.uf2`
- size: **895,488 bytes**
- SHA-256: `bc3cc1501fbc7fd145758b07070abafbf734d30869b4d64799bc8a6054b29f25`
- CI run: `35980402577` — host/architecture PASS; Pico 2 W production PASS.

## Required scenarios

1. From each HOME state where `SAVED DEVICES` is available, enter it and confirm the canonical layout.
2. Confirm exactly one saved Mouse is represented per page.
3. Confirm title format `N OF COUNT`.
4. On the currently connected Mouse page, confirm its real name is shown and the name is cyan.
5. Confirm row 2 is `STATUS: CONNECTED` on the connected page.
6. Move to another saved page with Joy Right/Left and confirm `STATUS: DISCONNECTED`.
7. A disconnected saved Mouse without a persisted name may display `UNKNOWN MOUSE`; no fake device name may appear.
8. Confirm `PROFILE: PASSTHROUGH/STANDARD/ESCAPE/CUSTOM` matches the active runtime profile.
9. Confirm Joy Left/Right wrap through all saved pages.
10. Confirm Joy Up/Down do not select legacy list rows.
11. Confirm `REMOVE DEVICE` is visually present as the next action but Joy Press does not leave `saved-devices` in HOPE-04.
12. Confirm Key B returns through the correct HOME resolver: connected -> home-connected, saved offline -> home-searching.
13. Confirm Key Y locks and any key unlocks through the existing HOME resolver.
14. Disconnect/reconnect the current Mouse while on `saved-devices`; the screen stays usable and connection status reflects runtime state.
15. Confirm Pair New, Mouse movement, buttons, scroll and all already accepted remapping profiles still work.
16. Confirm no Keyboard/Composite pairing option or legacy saved-device detail screen is reachable.

## Operator result

**PENDING**.
