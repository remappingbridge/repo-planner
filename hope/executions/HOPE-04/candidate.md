# HOPE-04 — candidate

Status: **IMPLEMENTED / AUTOMATED PASS / PHYSICAL ACCEPTANCE PENDING**.

- branch: `hope/hope-04-saved-devices`
- draft PR: `#16`
- accepted base: `5c62a98a4495570237d7b4b223a090bea1020990`
- current implementation head: `4e738622a06a572589f4626a9a7454c486efbe62`

## Implemented behavior

- canonical Mouse UI v1 `saved-devices` shell;
- one saved Mouse per page;
- connected Mouse is always projected as page `1 OF N`, regardless of its LE Device DB/bond index;
- when the connected Mouse disconnects, its saved name and front-page identity are retained;
- `N OF COUNT` title;
- connected Mouse page uses the real current GATT Device Name and cyan name;
- saved Mouse names are persisted by bond identity and remain visible after disconnect; only a pre-existing bond whose name has never yet been learned by this firmware may temporarily use `UNKNOWN MOUSE`;
- connected/disconnected status is dynamic;
- profile row reflects the accepted runtime profile;
- page navigation wraps with Joy Left/Right;
- Key B resolves HOME;
- Key Y keeps global lock;
- `JOY PRESS: ACCESS` is intentionally inert pending HOPE-05;
- legacy Keyboard/Composite pairing and old saved-detail/remove UX paths are removed.

## Automated evidence

- corrected GitHub Actions run: `#64` / `35982729822`
- `host-architecture`: PASS
- host/architecture test suite: PASS
- `pico2-w-production`: PASS
- production UF2 verification: PASS
- corrected UF2: `HOPE-04-saved-devices-name-fix-3b85bac-pico2w.uf2`
- UF2 size: **895,488 bytes**
- UF2 SHA-256: `fe481023d13f29300f061e081ec54a05706b87ba50ee8f98f0c1d9949c98c256`
- previous run #63 / candidate `143c00b...`: SUPERSEDED

Do not promote before the operator physically accepts this exact candidate UF2.


## Physical-test bugfix — saved page name

Physical testing reported that the connected Mouse name was correct on `home-connected` but was not shown on the corresponding `saved-devices` page.

Root cause: `home-connected` consumes the current GATT Device Name directly, while the saved page first needs to identify which bond/page is the current Mouse. The initial HOPE-04 implementation compared the live GAP peer address against the persisted LE bond address. That is not reliable with BLE Privacy/RPA because the connection can expose a resolvable private address while the bond stores the identity address.

Correction: the saved-page association now uses BTstack Security Manager `sm_le_device_index(g_connection_handle)`, which is the authoritative mapping from the live connection to the LE Device DB bond.

Corrected candidate head: `3b85bac57189b7dc92b8bc564d2ddf983d8e4520`.

The previous physical candidate `143c00b053e22345768054786a3f334d995bbb60` is superseded and must not be accepted.


## Second physical-test correction — authoritative saved-page index

The previous correction still rendered `UNKNOWN MOUSE` in physical testing. The name itself remained correct on `home-connected`; therefore the failure was again the saved-page association, not GATT Device Name acquisition.

The corrected implementation no longer depends only on querying `sm_le_device_index()` after the connection is already ready. It now caches the LE Device DB index at the moment BTstack resolves the peer identity:

- `SM_EVENT_IDENTITY_RESOLVING_SUCCEEDED` -> `sm_event_identity_resolving_succeeded_get_index(packet)`;
- newly paired Mouse -> newly created LE Device DB index during Pair New promotion;
- defensive single-device fallback -> page 0 when exactly one Mouse is saved.

Corrected implementation head: `8980f39705f79ef9a0a49b4ee04450e64ca5d761`.

Superseded candidates: `143c00b...` and `3b85bac...`.


## Final physical-test correction — connected-first ordering and persistent names

Physical testing of `8980f397...` exposed two remaining issues:

1. the connected Mouse was shown on its raw bond-index page instead of always being page 1;
2. disconnect cleared the connection-scoped `current_mouse_name`, causing the saved page to fall back to `UNKNOWN MOUSE`.

The corrected implementation separates saved identity from connection state:

- saved names are persisted in BTstack TLV under tag `B2SN`, keyed by the bond identity address;
- a learned Device Name is stored when a bonded Mouse connects or Pair New promotes it;
- `saved_front_bond` controls presentation order independently from the raw LE Device DB index;
- connecting any saved Mouse moves that Mouse to page 1 immediately;
- disconnect clears only the HOME/current connection name and keeps the saved registry name;
- the last front Mouse remains page 1 after disconnect until another Mouse becomes connected;
- other saved Mice keep their names on their respective pages;
- names are reloaded from TLV after reboot and are not intended to disappear until manual removal is implemented by HOPE-05.

Final corrected candidate:

- commit: `4e738622a06a572589f4626a9a7454c486efbe62`
- CI: `#73` / `35985679522`
- host/architecture: PASS
- Pico 2 W production: PASS
- UF2: `HOPE-04-saved-devices-persistent-names-connected-first-4e73862-pico2w.uf2`
- size: **900,608 bytes**
- SHA-256: `15208ed71da728e69038b3bd15d19699e194e9974f84df3a108c5e9d3103617e`

All earlier HOPE-04 candidates are superseded.
