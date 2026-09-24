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
