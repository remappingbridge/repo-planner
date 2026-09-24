# HOPE-04 — candidate

Status: **IMPLEMENTED / CI VALIDATION IN PROGRESS / PHYSICAL ACCEPTANCE PENDING**.

- branch: `hope/hope-04-saved-devices`
- draft PR: `#16`
- accepted base: `5c62a98a4495570237d7b4b223a090bea1020990`
- current implementation head: `37fe5540cf76bd7fbc840add2e81766682cc171a`

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

Do not promote before CI succeeds and the operator physically accepts the exact candidate UF2.
