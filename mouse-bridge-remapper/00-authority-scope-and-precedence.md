# Mouse Bridge Remapper — authority, scope and precedence

Status: **PLANNING ONLY / NO MBR GATE EXECUTED**.

This directory is the planning authority for `tiagooliveirajs/mouse-bridge-remapper`.

## 1. Objective

Build a Raspberry Pi Pico 2 W / RP2350 appliance dedicated to Bluetooth Mouse bridging and Mouse button remapping, starting from the behavior physically accepted in BLU2USB through G06 and adapting it to the current Mouse Bridge Remapper product contract.

The product may keep multiple mice saved, but only **one mouse may be connected at a time**.

The destination is intentionally not a Keyboard/Composite fork of BLU2USB. The Bluetooth product domain is Mouse only.

## 2. Source-of-truth precedence

When requirements overlap, use this order:

1. **Current Mouse Bridge Remapper product decisions**, including `requirements/2026-09-20-single-connected-mouse.md` and the current documentation in `tiagooliveirajs/mouse-bridge-remapper`.
2. `requirements/2026-09-19-user-rules.md` for layout and behavior not superseded by later decisions.
3. **Physically accepted BLU2USB G06 behavior** at `tiagooliveirajs/blu2usb`, branch `gate/g06-profiles-remap-logitech-hidpp`, accepted SHA `7eee024ad4ee726c5a85ffa2f32b9f47187878af`, PR #8.
4. Earlier accepted BLU2USB G01-G05 interaction, renderer, USB, canonical-HID and BLE-Mouse corrections inherited by G06, only where they do not conflict with current MBR rules.
5. `tiagooliveirajs/infra-planner` historical planning as migration/process evidence only.
6. BLU2USB G07+ Keyboard work, rejected candidates and old POCs as research evidence only.

A contradiction inside a higher-priority source is not resolved by silently falling back to a lower-priority source. It belongs in `02-ambiguity-register.md` and blocks only dependent implementation work.

## 3. Immutable G06 reference

Migration baseline:

- repo: `tiagooliveirajs/blu2usb`;
- branch: `gate/g06-profiles-remap-logitech-hidpp`;
- accepted head: `7eee024ad4ee726c5a85ffa2f32b9f47187878af`;
- accepted production UF2 SHA-256: `d32819b99ec349cc36882aaeaf9e84000a98dc3dcd86238ed364fd01301c8f88`;
- physical acceptance: applicable G06 Mouse/profile/persistence/reconnect/HID++/UI/USB scenarios PASS on that exact candidate.

The baseline is behavioral and architectural evidence. No MBR gate may wholesale-copy G06 and call the migration complete.

## 4. Hard scope

### Included

- BLE HOGP Mouse discovery, classification, security/bonding, reconnect and forwarding;
- multiple saved mice;
- zero or one live connected mouse;
- canonical Mouse button/motion/wheel/pan events;
- Mouse profiles/remapping;
- Custom remap editing and persistence;
- Logitech Lift/HID++ correction where needed;
- device removal and Mouse credential/profile cleanup;
- Waveshare HAT/LCD interaction, lock/unlock, help and Learn presentation;
- stable USB Mouse output;
- minimal USB Keyboard output used only for synthetic Escape from mouse remapping;
- product-state persistence separated from Bluetooth credentials.

### Explicitly excluded

- more than one ready/live Mouse session;
- multi-connected HOME/count UI;
- cross-mouse HID aggregation;
- simultaneous BLE Mouse capacity qualification;
- physical Bluetooth Keyboard pairing/input;
- Bluetooth Classic HID Keyboard;
- BLE HOGP Keyboard as a product device;
- Bluetooth Composite Mouse+Keyboard as a product type;
- Keyboard/Composite registries or pairing/detail screens;
- `classic_hid`, `keyboard_transport` and Composite production modules;
- importing G07 Keyboard implementation as production code;
- diagnostic USB CDC, debug PID/personality, UART-dependent acceptance or debug-only product UI.

## 5. Escape exception

Escape is explicitly retained.

A mouse button may produce a standard USB Keyboard Escape key through a minimal fixed firmware-owned output path.

This does **not** authorize Bluetooth Keyboard discovery, pairing, saved records, general keyboard input/remapping or Composite product support.

## 6. Single-live-session product rule

The runtime truth is:

```text
saved_mice = 0..N persistent records
live_mouse = None | one MouseSession
```

No code path may publish a second ready mouse before the old live session has been safely released and disconnected.

`PAIR NEW MOUSE` is a replacement transaction, not an additive connection transaction.

## 7. HOME/search rule

HOME is resolved uniformly:

```text
no saved mouse -> searching-first
saved mice + live mouse -> home-connected
saved mice + no live mouse -> home-searching + automatic bounded saved search
```

Saved search accepts the first saved mouse that reaches ready state and stops.

If search times out, HOME becomes `DEVICE NOT FOUND`.

If the connected mouse powers off/disconnects, the same HOME resolution automatically starts saved search. There is no separate hidden reconnect policy.

## 8. Meaning of “migrate through G06”

Migration means preserving every applicable accepted Mouse behavior through G06 while adapting it to the current single-live-session product and new UX.

Use three classifications:

- **PRESERVE** — behavior remains unchanged unless explicitly superseded.
- **ADAPT** — accepted behavior remains required but is reshaped for the current product architecture/UX.
- **EXCLUDE** — behavior is intentionally outside scope.

The detailed ledger is `01-g06-migration-ledger.md`.

## 9. Gate naming and execution state

Implementation gates are named `mbr-00`, `mbr-01`, ... as defined in `05-gates.md`.

At this planning revision:

- every gate is **PLANNED**;
- no gate is started, satisfied or accepted;
- no firmware implementation is authorized by this documentation update;
- no accepted UF2 or physical claim is created.

## 10. No-regression principle

A later gate may add capability but may not silently weaken an accepted predecessor invariant.

In particular, simplifying to one live mouse must not regress:

- release safety;
- generic Mouse fallback;
- bonded reconnect;
- LCD/HAT responsiveness;
- persistence integrity;
- Logitech Forward hold behavior;
- fixed USB identity;
- Keyboard/Composite exclusions.

A true contract contradiction stops dependent implementation work and is documented. Build convenience is never authority to change product behavior.
