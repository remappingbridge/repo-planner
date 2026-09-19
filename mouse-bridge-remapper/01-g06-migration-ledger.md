# G06 migration and no-regression ledger

Status: **PLANNED**. Source baseline: BLU2USB G06 accepted SHA `7eee024ad4ee726c5a85ffa2f32b9f47187878af`.

This ledger defines what Mouse Bridge Remapper must preserve, adapt or exclude from the accepted BLU2USB history. It is deliberately stricter than a source-file copy list: observable behavior and failure protections are what matter.

## 1. PRESERVE — interaction and visual lessons

Unless the 2026-09-19 Mouse Bridge Remapper rules explicitly replace a screen or control, preserve these accepted G03-G06 rules:

- HAT actions execute on **release**, not initial press.
- Pressed visible controls turn white while held; their action occurs only after release.
- Help owns interaction: `ANY KEY: BACK` consumes the control and returns to the owning page; Key Y must not accidentally lock while Help owns input.
- Lock affects display/presentation only. Mouse forwarding, Bluetooth, reconnect and remap continue while locked.
- The first complete HAT interaction while locked is consumed only to unlock, restore display and navigate to the product home state; it cannot also activate another command.
- Selection white has priority over cyan/current/connected state; when selection leaves, a still-current row returns to cyan.
- Static/example body text uses the accepted off-white-yellow semantics; resting options are gray; selected/pressed options are white; positive/current/connected state is cyan unless a new screen explicitly overrides it.
- The accepted Waveshare 240x240 renderer geometry and G03 pixel-relocation rules remain the starting renderer contract. New explicit character coordinates override old character coordinates for the affected screen only.
- Pixel positions are tested from the actual target string/token, never from the first coincidental character in the row. This preserves the G02 lesson where an assertion accidentally matched the `K` in `LOCK/UNLOCK` instead of the `KEY` label.
- Literal screen wording and character coordinates require regression tests/golden projections before physical validation.

## 2. PRESERVE — canonical Mouse input safety

The strongest reusable G05/G06 invariant is source-aware ownership. Preserve it and extend source identity from “BLE Mouse” to individual connected mice.

- Transport-specific report layouts never enter USB/application-domain logic.
- Report Map must classify the peer as Mouse before it is accepted.
- Decode canonical buttons, signed relative X/Y, vertical wheel and horizontal pan.
- Normalize the accepted duplicated Report-ID framing variants and reject malformed/truncated/shifted frames.
- Persistent button ownership is idempotent: duplicate press/release cannot corrupt state.
- A target Mouse button remains held until the last source that owns it releases it.
- Disconnect, parser/runtime failure, queue overflow, profile transition and device removal release **only the affected source's** ownership.
- Relative X/Y/wheel/pan are transient. They are accumulated in bounded form and consumed only after the USB owner accepts the corresponding report/chunk.
- Runtime failure must fail release-safe rather than leave the host with a stuck button.
- LCD lock never pauses Mouse forwarding.

### Multi-Mouse adaptation

Every live Mouse session gets a stable runtime `MouseSourceId` derived from the persistent device identity plus a connection generation. Late events from an obsolete generation must not affect a newly reconnected session.

For a target button, aggregation is conceptually `target -> set(source owners)`, not “last report wins”. Disconnecting Mouse A must not release a target still held by Mouse B.

## 3. PRESERVE — BLE HOGP behavior

- BLE HOGP remains the accepted Mouse transport baseline.
- Security/bonding and Report Protocol remain transport concerns, not UI concerns.
- Generic mice must function without Logitech-specific behavior.
- HIDS/report/security errors recover through bounded state transitions; they do not trap the main UI/USB loop.
- Previously bonded peers are restored through BTstack credential state and attempted before generic discovery according to reconnect policy.
- The G06 bonded reconnect path used resolving/accept-list data and a bounded attempt (8 seconds in G06) before generic fallback. The new multi-Mouse coordinator may adapt scheduling, but it may not regress into an unbounded “missing saved peer blocks all other mice” state.

## 4. PRESERVE — Mouse profiles and exact preset semantics

The inherited profile families are:

- `PASSTHROUGH`;
- G06 `DEFAULT REMAP`, renamed by the new rules in some places to `STANDARD REMAP` — naming remains unresolved in `AMB-006`;
- `ESCAPE REMAP` — behavior blocked by `AMB-001` because the new project excludes Keyboard/USB Composite;
- `CUSTOM REMAP`.

Inherited exact preset mapping semantics:

| Source button | Passthrough | Default/Standard | Escape |
|---|---|---|---|
| Left | Left | Forward | Escape |
| Right | Right | Backward | Backward |
| Middle | Middle | Middle | Forward |
| Forward | Forward | Left | Left |
| Backward | Backward | Right | Right |

Movement, vertical wheel and horizontal pan are never altered by button profiles.

A profile change must release stale old-profile ownership before the new mapping becomes authoritative. UI success may appear only after runtime acceptance and the required persistent commit succeed.

## 5. PRESERVE/ADAPT — CustomTemplate semantics

Unless explicitly superseded later:

- retain one persistent Pico-global `CustomTemplate`;
- sources are Left, Right, Middle, Forward and Backward;
- Mouse-button targets remain Left, Right, Middle, Forward and Backward;
- Escape remains listed by the supplied new UX but is blocked by `AMB-001` until the output contradiction is resolved;
- a `WILL BECOME` selection updates the draft immediately and the returning Custom editor must show that draft value;
- the accepted draft survives reboot even before full `APPLY CUSTOM`;
- applying another preset does not erase the Custom draft;
- the last actually applied profile remains active across reboot while an unapplied dirty Custom draft is restored separately;
- successful `APPLY CUSTOM` requires runtime + persistent confirmation before success feedback.

### Multi-Mouse adaptation

The profile **kind** becomes per saved Mouse. The global CustomTemplate remains shared unless a later explicit decision changes it. A Mouse whose profile kind is Custom references that global template.

How the new `home-connected` page chooses which connected Mouse receives a profile change is unresolved by the supplied UX and is tracked in `AMB-002`/`AMB-003`.

## 6. PRESERVE — product persistence quality

Carry forward the G06 power-loss lessons:

- schema-versioned product state;
- integrity protection (CRC or stronger equivalent);
- two alternating generations/slots or an equivalently power-loss-safe strategy;
- a corrupt/torn newest generation falls back to the previous valid generation;
- product-state flash is separated from BTstack credential storage;
- boot reconstructs profiles/remap/UI/vendor-fix requirements before input becomes authoritative;
- no valid product record falls back to safe defaults.

The new persistent schema additionally needs, per Mouse:

- stable device identity sufficient to correlate the product record with BTstack credentials;
- display name/model text when available;
- saved state;
- per-device profile kind;
- capability/vendor metadata required for automatic HID++ behavior;
- no Keyboard or Composite logical type.

A device removal transaction must coordinate application record deletion, profile association cleanup, live-source release/disconnect and Bluetooth credential removal without leaving a half-removed Mouse.

## 7. PRESERVE — Logitech Lift HID++

- HID++ remains an automatic vendor backend; never expose it as a user-selectable transport/profile.
- Preserve the accepted `REPROG_CONTROLS_V4` feature `0x1b04` / Forward CID `0x0056` behavior where supported and needed.
- When Forward is remapped and diversion is acknowledged, true physical down/hold/up must survive so Forward->Left dragging works for the full hold.
- Passthrough must remove/not require Forward diversion and restore native Forward behavior.
- Unsupported/non-Logitech peers fail safe to Standard HID without breaking generic input.
- Disconnect/profile change releases vendor-derived held ownership.

### Multi-Mouse adaptation

HID++ state is per Mouse session. Probing/diversion for Mouse A may not alter Mouse B. Vendor responses must be correlated to the correct connection/session generation.

## 8. ADAPT — live connection model

G06 had one logical active Mouse. The new product explicitly permits multiple simultaneous connected mice.

Replace singular `active_mouse` as transport truth with:

- `saved_mice`: persistent collection;
- `sessions`: zero or more simultaneous live Mouse sessions;
- `connected_mouse_ids`: runtime set;
- optional UI `focused_mouse_id`: presentation/editing context only, never the authority for whether other mice remain connected or forward input.

Runtime `CONNECTED`/`DISCONNECTED` events remain authoritative; recent movement is never a connection proxy.

The rule for choosing `focused_mouse_id` is **not yet defined** by the supplied UX and must not be invented during implementation.

## 9. ADAPT — reconnect behavior

G06 preferred one bonded Mouse, then fell back to generic scan. New requirements distinguish:

- no saved mice -> search for first new Mouse indefinitely/logically indefinitely;
- saved mice exist -> bounded search for saved Mouse(s), then `home-retry` if none found;
- explicit Pair New -> search only for a Mouse not already saved, then retry page if none is found.

What “search saved” means when several saved mice exist — first match only vs reconnect as many saved peers as possible — is unresolved in `AMB-004`. The runtime must be designed to support a policy without coupling that decision into the BLE parser.

## 10. ADAPT — USB identity

Preserve G04/G06 structural safety:

- USB descriptor is firmware-owned and stable from boot;
- Bluetooth connect/disconnect, pairing, profile changes, lock/unlock and reconnect never force USB re-enumeration;
- `usb_hid`/`usb_mouse` is the only descriptor/report owner;
- no CDC/MSC/MIDI/vendor debug interface.

But do **not** preserve the literal old BLU2USB Mouse+Keyboard descriptor: the new project declares Mouse-only scope. Exact VID/PID/product strings and the Escape contradiction are unresolved (`AMB-001`, `AMB-018`).

## 11. ADAPT — UX

The new `requirements/2026-09-19-user-rules.md` replaces the old BLU2USB page hierarchy for the screens it specifies. In particular:

- the old `HOME`, `STATUS`, `OTHER DEVICES STATUS`, `OTHER OPTIONS`, Keyboard and Composite pages are not retained as product screens;
- new boot roots are `searching-first` when no Mouse is saved and `home-searching` when at least one Mouse is saved;
- new `home-connected`, per-Mouse `saved-devices`, Pair New and retry/help flows replace the old G06 discovery/status hierarchy;
- new explicit character coordinates override the old Learn coordinates on affected new screens, once internal contradictions are resolved.

Accepted renderer/color/release/lock/help behavior remains inherited when the new rule set does not override it.

## 12. EXCLUDE — Keyboard/Composite implementation

Never import as production behavior:

- BLU2USB G07 Classic Keyboard branches;
- `classic_hid`;
- `keyboard_transport`;
- BLE Keyboard product classification;
- physical Keyboard canonical sources;
- Composite logical device support;
- Keyboard/Composite saved/preferred/active registries;
- Keyboard/Composite pairing and detail pages.

Synthetic Escape from a Mouse is not silently exempted from this rule. It is exactly the unresolved conflict in `AMB-001`.

## 13. Bugs/corrections that must not be rediscovered

The new project must start with regression coverage for these known historical mistakes/corrections:

1. **Pixel relocation regression:** semantic row numbers do not justify reverting to naive uniform pixel placement. Preserve accepted G03 relocation rules and assert new screen coordinates.
2. **Learn wording/coordinate drift:** old accepted title/coordinates changed multiple times. The new contract must freeze one literal string and token position before implementation.
3. **Wrong-help-key typo:** historical `KEY C: HELP` had to become physical `KEY X: HELP`; do not infer labels independently from HAT wiring.
4. **Action-on-press bug class:** all actions remain release-triggered; a press is visual feedback only.
5. **Back-stack bug class:** G06 corrected feedback pages so one Back returned to the logical parent rather than an obsolete Apply page. New navigation must explicitly model feedback-state return targets.
6. **Selection/current-color collision:** selected current rows are white; when selection leaves they return to cyan.
7. **Stale connection display:** UI updates from connection events immediately, not from the next HAT event or Mouse movement.
8. **Static-profile display:** status/home projection comes from confirmed/restored runtime profile, not a hard-coded default.
9. **Optimistic Apply:** do not show applied/success state before runtime + persistence confirmation.
10. **Custom draft stale UI:** returning from a per-button editor must render the just-accepted draft immediately.
11. **Custom draft reboot loss:** accepted-but-not-applied draft is distinct persistent state.
12. **Bonded Logitech reconnect:** restore resolving/accept-list-based bonded reconnect before generic fallback so Lift can reconnect after Pico power loss without forced fresh pairing.
13. **HID Report-ID framing:** normalize known duplicated framing and reject malformed lengths; do not shift fields silently.
14. **Stuck ownership on failure:** disconnect, queue overflow, profile change and removal are source-scoped release events.
15. **Logitech Forward click-only behavior:** HID++ diversion must preserve down/hold/up, not synthesize a momentary click, so drag works.
16. **Toolchain bootstrap traps:** normalize external Pico SDK path handling and allow SDK-required GNU extensions rather than misdiagnosing SDK source `asm` as product code failure.

Every applicable item above belongs in predecessor regressions of later gates.
