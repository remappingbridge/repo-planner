# G06 migration and no-regression ledger

Status: **PLANNED**. Source baseline: BLU2USB G06 accepted SHA `7eee024ad4ee726c5a85ffa2f32b9f47187878af`.

This ledger defines what Mouse Bridge Remapper must preserve, adapt or exclude from accepted BLU2USB history. Observable behavior and failure protections matter more than source-file similarity.

## 1. PRESERVE — interaction and visual lessons

Unless current Mouse Bridge Remapper documentation explicitly replaces a screen/control:

- HAT actions execute on **release**, not initial press.
- Pressed visible controls turn white while held; actions occur after release.
- Help owns interaction: `ANY KEY: BACK` consumes the input and returns to the owning page.
- Lock affects display/presentation only. Mouse forwarding, Bluetooth, reconnect and remap continue while locked.
- The first complete HAT interaction while locked is consumed only to unlock and cannot also activate another command.
- Selection white has priority over cyan/current/connected state; leaving selection restores cyan if still current.
- Static/body text, resting options, selected/pressed options and connected/current state retain accepted semantic colors unless a new screen explicitly overrides them.
- Accepted Waveshare 240x240 geometry and G03 pixel-relocation lessons remain the renderer baseline.
- Token-position tests target the actual token, preserving the historical lesson where a test matched the wrong `K`.
- Literal screen wording/coordinates require host golden tests before physical validation.

## 2. PRESERVE — canonical Mouse input safety

The current product has only one live Mouse session, but it still needs the G05/G06 release-safety model.

Preserve:

- transport-specific report layouts never enter USB/UI/profile domain logic;
- Report Map classifies a peer as Mouse before acceptance;
- canonical buttons, signed X/Y, vertical wheel and horizontal pan;
- duplicated Report-ID framing normalization and malformed/truncated-frame rejection;
- idempotent button press/release handling;
- bounded relative-event buffering consumed only after USB submission is accepted;
- disconnect/parser failure/queue continuity loss/profile transition/removal are release-safe;
- LCD lock never pauses Mouse forwarding.

### Single-session adaptation

The runtime carries one optional `MouseSessionId` with a generation/token. Late callbacks from an obsolete generation must not affect a replacement session.

Cross-mouse ownership sets are **not** required. However, two physical buttons on the same mouse may map to the same output target, so held-state ownership/refcounting must still prevent premature release within the current session.

## 3. PRESERVE — BLE HOGP behavior

- BLE HOGP remains the accepted Mouse transport baseline.
- Security/bonding and Report Protocol remain transport concerns, not UI concerns.
- Generic mice must function without Logitech-specific behavior.
- HIDS/report/security errors recover through bounded state transitions; they do not trap the main UI/USB loop.
- Previously bonded peers are reused through BTstack credential state.
- Saved reconnect is bounded so an absent peer cannot block HOME indefinitely.

G06 used resolving/accept-list state and an 8-second preferred bonded reconnect window. The exact new timeout remains a product constant to freeze, but the bounded-reconnect lesson is preserved.

## 4. PRESERVE — profiles and exact mappings

Profiles retained:

- `PASSTHROUGH`;
- `DEFAULT/STANDARD REMAP`;
- `ESCAPE REMAP`;
- `CUSTOM REMAP`.

Exact preset semantics:

| Source button | Passthrough | Default/Standard | Escape |
|---|---|---|---|
| Left | Left | Forward | Escape |
| Right | Right | Backward | Backward |
| Middle | Middle | Middle | Forward |
| Forward | Forward | Left | Left |
| Backward | Backward | Right | Right |

Movement, vertical wheel and horizontal pan are never altered by button profiles.

Profile change releases stale held state from the old mapping before the new mapping becomes authoritative. UI success appears only after runtime acceptance and required persistent commit succeed.

## 5. PRESERVE/ADAPT — CustomTemplate semantics

Unless explicitly superseded:

- retain one persistent Pico-global `CustomTemplate`;
- sources: Left, Right, Middle, Forward, Backward;
- targets: Left, Right, Middle, Escape, Forward, Backward;
- per-source Apply-and-Back updates the draft immediately;
- returning to Custom editor reflects the just-accepted draft;
- dirty/unapplied draft survives reboot independently of the last active profile;
- applying another preset does not erase the draft;
- successful full Apply requires runtime + persistence confirmation.

Profile **kind** remains per saved Mouse. The global Custom template is shared by any saved Mouse whose confirmed profile kind is Custom.

Because only one Mouse is live, HOME/remapper actions always target the connected Mouse; there is no focus-selection ambiguity.

## 6. PRESERVE — persistence quality

Carry forward:

- schema-versioned product state;
- integrity protection;
- two alternating generations/slots or equivalent power-loss-safe strategy;
- corrupt/torn newest generation falls back to previous valid generation;
- product state is separated from BTstack credential storage;
- boot reconstructs profiles/remap/UI/vendor requirements before input becomes authoritative;
- no valid product record falls back to safe defaults.

Per saved Mouse persist:

- stable device identity;
- display name/model when available;
- confirmed profile kind;
- capability/vendor metadata required for safe behavior;
- no Keyboard/Composite logical type.

Transient live session state is not persisted as an active connection.

## 7. PRESERVE — Logitech Lift HID++

- HID++ remains an automatic vendor backend, never a user-selectable transport/profile.
- Preserve accepted `REPROG_CONTROLS_V4` feature `0x1b04` / Forward CID `0x0056` behavior where needed.
- Forward remapping preserves physical down/hold/up so dragging works.
- Passthrough removes/unrequires Forward diversion and restores native Forward.
- Unsupported/non-Logitech peers fail safe to Standard HID.
- Disconnect/profile change releases vendor-derived held state.

HID++ state belongs only to the current session and is discarded safely when that session is replaced.

## 8. ADAPT — live connection model

The current product intentionally uses:

```text
saved_mice: persistent collection
live_mouse: None | one MouseSession
```

`CONNECTED`/`DISCONNECTED` events remain authoritative; recent motion is never a connection proxy.

There is no:

- `connected_mouse_ids` set;
- multi-live focus Mouse;
- simultaneous HIDS-session requirement;
- `N DEVICES CONNECTED` state;
- simultaneous-Mouse capacity target.

## 9. ADAPT — HOME and reconnect behavior

Current policy:

- no saved mice -> logically indefinite first new-Mouse search;
- saved mice + live Mouse -> `home-connected`;
- saved mice + no live Mouse -> `home-searching` and automatic bounded saved-device search;
- first saved Mouse that becomes ready wins and search stops;
- saved search timeout -> `home-retry` / `DEVICE NOT FOUND`;
- connected Mouse disconnect/power-off -> same HOME resolver starts saved search automatically;
- `KEY A: RETRY SEARCH` starts a fresh bounded saved search from `home-retry`.

This replaces older planning questions about reconnecting several saved mice simultaneously.

## 10. ADAPT — Pair New

Pair New is a **replacement** transaction.

If a Mouse is connected:

1. stop accepting new events from it;
2. release held Mouse/Escape state;
3. disconnect it;
4. keep its saved record and bond;
5. search for one unsaved Mouse;
6. first valid accepted candidate becomes the sole live Mouse;
7. stop the transaction.

Pair New failure/cancel does not delete the old saved Mouse and does not silently reconnect it. Returning to HOME with no live session invokes the normal saved search.

## 11. ADAPT — USB identity

Preserve G04/G06 structural safety:

- USB descriptor is firmware-owned and stable from boot;
- Bluetooth/profile/UI state never forces USB re-enumeration;
- one USB module owns descriptors/report submission;
- no diagnostic CDC/MSC/MIDI/vendor-debug interface.

Current scope explicitly permits Mouse HID plus a minimal Keyboard output capability solely for synthetic Escape. This does not authorize Bluetooth Keyboard support.

Exact project VID/PID/product strings remain to be frozen.

## 12. ADAPT — UX

The current `mouse-bridge-remapper` documentation is the product-facing UX authority. Key lifecycle rules:

- `searching-first` when no Mouse is saved;
- `home-connected` always shows the one live Mouse name/profile;
- `home-searching` is entered whenever HOME has saved mice but no live connection and starts saved search automatically;
- `home-retry` appears after saved-search timeout;
- Pair New replaces, rather than adds to, the current live connection;
- Saved Devices has one saved Mouse per page; at most one page can be connected/cyan;
- no multi-connected count/focus UI exists.

Accepted renderer/color/release/lock/help behavior remains inherited where not superseded.

## 13. EXCLUDE — Keyboard/Composite and obsolete multi-Mouse machinery

Never import as production behavior:

- BLU2USB G07 Classic Keyboard branches;
- `classic_hid` / `keyboard_transport`;
- BLE Keyboard product classification;
- Composite logical device support;
- Keyboard/Composite registries/pairing/detail pages;
- multiple simultaneously ready Mouse sessions;
- cross-mouse button aggregation;
- multi-Mouse UI focus/selection;
- simultaneous-BLE Mouse capacity experiments/gates.

Synthetic Escape output is the only Keyboard-related exception.

## 14. Historical bugs/corrections that must not be rediscovered

1. Pixel relocation regression: preserve accepted physical geometry and assert new screen coordinates.
2. Learn wording/coordinate drift: freeze literal text and token positions before rendering implementation.
3. Wrong-help-key typo: physical Help is Key X where documented; never infer labels independently from HAT wiring.
4. Action-on-press bug class: actions remain release-triggered.
5. Back-stack bug class: explicit logical parent transitions; no obsolete Apply-page detours.
6. Selection/current-color collision: selected current rows white, return to cyan when selection leaves.
7. Stale connection display: UI updates immediately from runtime connection events.
8. Static-profile display: projection comes from confirmed/restored profile, not hard-coded default.
9. Optimistic Apply: no success before runtime + persistence confirmation.
10. Custom draft stale UI: returning from per-button editor shows accepted draft immediately.
11. Custom draft reboot loss: dirty unapplied draft is distinct persistent state.
12. Bonded Logitech reconnect: reuse bond/resolving data before bounded fallback behavior.
13. HID Report-ID framing: normalize accepted variants; reject malformed lengths.
14. Stuck output on failure: disconnect, queue failure, profile change, replacement and removal release safely.
15. Logitech Forward click-only behavior: preserve down/hold/up, not momentary click synthesis.
16. Toolchain bootstrap traps: normalize Pico SDK path handling and retain SDK-required GNU extensions.

Every applicable item above belongs in predecessor regressions of later gates.
