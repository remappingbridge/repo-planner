# HOPE-04 — candidate

Status: **IMPLEMENTED / AUTOMATED PASS / PHYSICAL ACCEPTANCE PENDING**.

- branch: `hope/hope-04-saved-devices`
- draft PR: `#16`
- accepted base: `5c62a98a4495570237d7b4b223a090bea1020990`
- current implementation head: `143c00b053e22345768054786a3f334d995bbb60`

## Implemented behavior

- canonical Mouse UI v1 `saved-devices` shell;
- one saved Mouse per page;
- `N OF COUNT` title;
- connected Mouse page uses the real current GATT Device Name and cyan name;
- disconnected pages use `UNKNOWN MOUSE` rather than invented names;
- connected/disconnected status is dynamic;
- profile row reflects the accepted runtime profile;
- page navigation wraps with Joy Left/Right;
- Key B resolves HOME;
- Key Y keeps global lock;
- `JOY PRESS: ACCESS` is intentionally inert pending HOPE-05;
- legacy Keyboard/Composite pairing and old saved-detail/remove UX paths are removed.

## Automated evidence

- GitHub Actions run: `#63` / `35980402577`
- `host-architecture`: PASS
- 37 host/architecture tests: PASS
- `pico2-w-production`: PASS
- production UF2 verification: PASS
- UF2 size: **895,488 bytes**
- UF2 SHA-256: `bc3cc1501fbc7fd145758b07070abafbf734d30869b4d64799bc8a6054b29f25`

Do not promote before the operator physically accepts this exact candidate UF2.


## Physical-test bugfix — saved page name

Physical testing reported that the connected Mouse name was correct on `home-connected` but was not shown on the corresponding `saved-devices` page.

Root cause: `home-connected` consumes the current GATT Device Name directly, while the saved page first needs to identify which bond/page is the current Mouse. The initial HOPE-04 implementation compared the live GAP peer address against the persisted LE bond address. That is not reliable with BLE Privacy/RPA because the connection can expose a resolvable private address while the bond stores the identity address.

Correction: the saved-page association now uses BTstack Security Manager `sm_le_device_index(g_connection_handle)`, which is the authoritative mapping from the live connection to the LE Device DB bond.

Corrected candidate head: `3b85bac57189b7dc92b8bc564d2ddf983d8e4520`.

The previous physical candidate `143c00b053e22345768054786a3f334d995bbb60` is superseded and must not be accepted.
