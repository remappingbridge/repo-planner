# HOPE-04 — saved-devices physical acceptance

Status: **PENDING OPERATOR TEST / NOT ACCEPTED**.

Candidate:

- branch: `hope/hope-04-saved-devices`
- commit: `4e738622a06a572589f4626a9a7454c486efbe62`
- draft PR: `#16`
- accepted base: `5c62a98a4495570237d7b4b223a090bea1020990`
- UF2: `HOPE-04-saved-devices-persistent-names-connected-first-4e73862-pico2w.uf2`
- size: **900,608 bytes**
- SHA-256: `15208ed71da728e69038b3bd15d19699e194e9974f84df3a108c5e9d3103617e`
- CI run: `35985679522` — host/architecture PASS; Pico 2 W production PASS.

## Required scenarios

1. From each HOME state where `SAVED DEVICES` is available, enter it and confirm the canonical layout.
2. Confirm exactly one saved Mouse is represented per page.
3. Confirm title format `N OF COUNT`.
4. Connect a Mouse whose raw saved/bond order is not first and confirm that it is nevertheless displayed on page `1 OF N`.
5. Confirm the connected Mouse real name is shown in cyan on page 1 and row 2 is `STATUS: CONNECTED`.
6. Move to another saved page with Joy Right/Left and confirm its saved name and `STATUS: DISCONNECTED`.
7. Disconnect the page-1 Mouse and confirm its name remains visible on its saved page with `STATUS: DISCONNECTED`; it must not revert to `UNKNOWN MOUSE`.
8. Connect a different saved Mouse and confirm that this newly connected Mouse immediately becomes page 1 while the previous Mouse moves to another page and keeps its name.
9. Reboot after at least two Mouse names have been learned and confirm those names are restored from persistent storage before manual removal.
10. Confirm `PROFILE: PASSTHROUGH/STANDARD/ESCAPE/CUSTOM` matches the active runtime profile.
11. Confirm Joy Left/Right wrap through all saved pages.
12. Confirm Joy Up/Down do not select legacy list rows.
13. Confirm `REMOVE DEVICE` is visually present as the next action but Joy Press does not leave `saved-devices` in HOPE-04.
14. Confirm Key B returns through the correct HOME resolver: connected -> home-connected, saved offline -> home-searching.
15. Confirm Key Y locks and any key unlocks through the existing HOME resolver.
16. Disconnect/reconnect the current Mouse while on `saved-devices`; the screen stays usable and connection status reflects runtime state.
17. Confirm Pair New, Mouse movement, buttons, scroll and all already accepted remapping profiles still work.
18. Confirm no Keyboard/Composite pairing option or legacy saved-device detail screen is reachable.


Superseded candidates: `143c00b...`, `3b85bac...`, `8980f397...` — do not use/accept.

## Operator result

**PENDING**. The connected-first/persistent-name candidate above must be physically tested.