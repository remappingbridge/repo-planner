# Mouse Bridge Remapper — authority, scope and precedence

Status: **PLANNING ONLY / NO MBR GATE EXECUTED**.

This directory is the complete planning authority for `tiagooliveirajs/mouse-bridge-remapper`. The implementation repository remains untouched by this planning work.

## 1. Objective

Build a Raspberry Pi Pico 2 W / RP2350 appliance dedicated to Bluetooth Mouse bridging and Mouse button remapping, starting from the behavior that was physically accepted in BLU2USB through G06 and replacing the old multi-device product UX with the new Mouse-only UX supplied on 2026-09-19.

The destination is intentionally **not** a Keyboard/Composite fork of BLU2USB. The product domain is Mouse only.

## 2. Source-of-truth precedence

When requirements overlap, use this order:

1. **Current Mouse Bridge Remapper rules** in `requirements/2026-09-19-user-rules.md` for every subject they explicitly redefine.
2. **Physically accepted BLU2USB G06 behavior** at `tiagooliveirajs/blu2usb`, branch `gate/g06-profiles-remap-logitech-hidpp`, accepted SHA `7eee024ad4ee726c5a85ffa2f32b9f47187878af`, PR #8.
3. Earlier accepted BLU2USB G01-G05 interaction, renderer, USB, canonical-HID and BLE-Mouse corrections inherited by G06, only where they do not conflict with item 1 or the Mouse-only scope.
4. `tiagooliveirajs/infra-planner` BLU2USB/fork planning as migration evidence and gate-process guidance. It does not override accepted code/behavior or the new rules.
5. BLU2USB G07 and later work, rejected/failed candidates, old POCs and historical branches are **research evidence only**. They are not product baselines for this repository.

A contradiction inside a higher-priority source is not resolved by silently falling back to a lower-priority source. It goes into `02-ambiguity-register.md` and blocks only the dependent implementation gate.

## 3. Immutable G06 reference

The migration baseline is exactly:

- repo: `tiagooliveirajs/blu2usb`;
- branch: `gate/g06-profiles-remap-logitech-hidpp`;
- accepted head: `7eee024ad4ee726c5a85ffa2f32b9f47187878af`;
- accepted production UF2 SHA-256 recorded by PR #8: `d32819b99ec349cc36882aaeaf9e84000a98dc3dcd86238ed364fd01301c8f88`;
- physical acceptance: all applicable G06 Mouse/profile/persistence/reconnect/HID++/UI/USB scenarios reported PASS on that exact candidate.

Normative G06 material to consult at execution time includes:

- `docs/product/00-product-contract.md`;
- `docs/technical/00-architecture-contract.md`;
- `docs/technical/03-g05-canonical-hid-validation.md`;
- `docs/technical/04-g06-profiles-remap-hidpp-validation.md`;
- `docs/ux/00-interaction-visual-contract.md`;
- `docs/ux/01-screen-layouts.md`;
- the production modules for domain, HOGP, HID aggregation, profiles/remap, Logitech HID++, storage, interaction, renderer/HAT and USB.

The baseline is behavioral and architectural evidence. No MBR gate is allowed to wholesale-copy the G06 tree and call the migration complete.

## 4. Hard Mouse-only scope

### Included

- BLE HOGP Mouse discovery, classification, security/bonding, reconnect and input forwarding;
- multiple saved mice;
- multiple simultaneously connected mice, subject to a physically qualified supported maximum;
- canonical Mouse button/motion/wheel/pan events;
- Mouse profiles/remapping;
- Custom remap editing and persistence;
- Logitech Lift/HID++ correction where needed;
- device removal and Mouse credential/profile cleanup;
- Waveshare HAT/LCD interaction, lock/unlock, help and Learn-the-Keys presentation;
- a stable USB Mouse-facing product identity;
- product-state persistence separated from Bluetooth credentials.

### Explicitly excluded

- physical Bluetooth Keyboard pairing or input;
- Bluetooth Classic HID Keyboard;
- BLE HOGP Keyboard as a logical product device;
- Bluetooth Composite Mouse+Keyboard devices as a product type;
- Keyboard device registry/preferred/active state;
- Composite device registry/preferred/active state;
- `PAIR KEYBOARD`, `PAIR COMPOSITE`, `OTHER DEVICES STATUS`, Keyboard/Composite saved/detail screens;
- `classic_hid`, `keyboard_transport` and Composite product modules;
- importing any G07 keyboard implementation as production code;
- diagnostic USB CDC, debug PID/personality, UART-dependent acceptance, debug-only LCD pages or parallel debug UF2 product variants.

### USB scope note

The intended new product is Mouse-only and therefore the target architecture contains one production USB HID Mouse function, not the old BLU2USB Mouse+Keyboard composite USB identity. However, the supplied new UX still contains Escape remapping. A standards-compliant USB Mouse interface cannot itself emit a Keyboard Escape key. This contradiction is deliberately unresolved here and is recorded as `AMB-001`; implementation of USB identity and Escape-dependent profiles is blocked until it is explicitly resolved.

## 5. Meaning of “migrate through G06”

Migration means preserving every applicable, accepted Mouse behavior through G06 while adapting it to the new Mouse-only product and new UX. It does **not** mean preserving obsolete product concepts.

Use three migration classifications:

- **PRESERVE** — accepted behavior remains unchanged unless explicitly superseded.
- **ADAPT** — accepted behavior remains a source requirement but must be redesigned for multi-Mouse or new UX.
- **EXCLUDE** — Keyboard/Composite-only behavior is intentionally not carried forward.

The detailed ledger is in `01-g06-migration-ledger.md`.

## 6. Gate naming and execution state

All implementation gates are named exactly `mbr-00`, `mbr-01`, `mbr-02`, ... as defined in `05-gates.md`.

At creation of this plan:

- every gate is **PLANNED**;
- no gate is started, satisfied or accepted;
- no implementation branch has been created by this planning task;
- no firmware was changed, built or flashed;
- no UF2 was produced;
- no physical claim was made.

## 7. No-regression principle

A later MBR gate may add capability but may not silently weaken an accepted predecessor invariant. In particular, multi-Mouse support is not accepted if it reintroduces stuck buttons, damages generic-Mouse fallback, loses bonded reconnect, regresses LCD/HAT responsiveness, corrupts persistence, or reintroduces Keyboard/Composite product scope.

A true contract contradiction stops the dependent gate and is documented. Build convenience is never authority to change product behavior.
