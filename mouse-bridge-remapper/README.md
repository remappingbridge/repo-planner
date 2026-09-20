# Mouse Bridge Remapper planning

Status: **MBR-02 IMPLEMENTED / ACCEPTANCE READY — connected HOME Pair New entry amended and revalidated pending CI/integration.**

This directory is the planning source of truth for `tiagooliveirajs/mouse-bridge-remapper`.

## Goal

Rebuild the Mouse portion of physically accepted BLU2USB through G06 as a cleaner Mouse-only Bluetooth product with:

- BLE HOGP Mouse transport only;
- multiple saved Mouse records;
- zero or one authoritative live Mouse;
- Pair New new-only replacement handoff;
- frozen Mouse remap profiles including synthetic Escape;
- HOME-driven saved reconnect;
- documentation as product, code as consequence.

Accepted historical migration reference:

- repo `tiagooliveirajs/blu2usb`;
- branch `gate/g06-profiles-remap-logitech-hidpp`;
- accepted SHA `7eee024ad4ee726c5a85ffa2f32b9f47187878af`;
- accepted UF2 SHA-256 `d32819b99ec349cc36882aaeaf9e84000a98dc3dcd86238ed364fd01301c8f88`.

G07+ Keyboard work is research evidence only.

## Current frozen product highlights

- one authoritative connected Mouse maximum;
- current Mouse stays live while Pair New searches;
- first valid unsaved replacement candidate triggers a safe handoff;
- Pair New timeout/cancel before handoff leaves current Mouse live;
- connected HOME presents the current Mouse name as title and exposes `PAIR NEW MOUSE` as its first visible option;
- connected HOME remap summary is the second option and opens `remapper-options`;
- connected HOME Saved Devices and Learn the Keys remain the third and fourth options;
- saved candidates are ignored as Pair New winners;
- to reconnect saved instead, unplug current Mouse and Back until HOME reaches SEARCHING;
- HOME with saved mice + no live Mouse starts 8-second saved search automatically;
- first-Mouse search repeats 8-second cycles until success;
- Pair New window is 15 seconds;
- canonical profile word is STANDARD;
- disconnected Saved Devices word is DISCONNECTED;
- names render first 21 supported characters, fallback `UNKNOWN MOUSE`;
- BLE HOGP only;
- Escape retained through minimal fixed USB Keyboard output;
- no Bluetooth Keyboard/Composite product support.

## Accepted implementation baseline

Accepted product `main` remains the MBR-01 baseline:

`tiagooliveirajs/mouse-bridge-remapper@cee10ee157ce0d7f6655df203327422c3c40e6b3`

MBR-01 established the compileable host/Pico 2 W scaffold, frozen module/dependency graph, single-authoritative-Mouse slot, separate non-authoritative Pair New candidate scaffold, architecture ownership guards and pinned G06-derived CI/toolchain baseline. The exact accepted implementation branch head was `826c50dab3b105c6bcecef6a6dbba401506aefc9`; exact-head CI run `35477941473` passed host/architecture and Pico 2 W production jobs.

## Current UX amendment

The 2026-09-20 connected-HOME amendment is recorded in `requirements/2026-09-20-connected-home-pair-new.md`. It supersedes only the former `home-connected` layout/reachability rule. The connected Mouse name is now the dynamic title; Pair New is the first visible option and directly opens `pair-new` without disconnecting the current Mouse.

## MBR-02 implementation state

MBR-02 implementation exists on product branch `mbr/mbr-02-host-ux-model`, PR #2. Exact implementation head at blocker discovery: `13cad3fec43eeed0353b0271a018012d115f2845`.

Host CI on that exact SHA passed all four contracts:

- `bootstrap_contract`;
- `ux_golden_contract`;
- `ux_behavior_contract`;
- `architecture_contract`.

The implementation covers the 30 canonical screens, release interaction, Help/Lock, HOME/search transactions, stale IDs, Pair New candidate/handoff semantics, name/status/profile projection, Custom draft, confirmation-only success and semantic colors.

Acceptance is now unblocked by the explicit connected-HOME amendment: `home-connected` has a visible `PAIR NEW MOUSE` first option that opens `pair-new` while the current Mouse remains live. The former blocker is retained as historical evidence and marked resolved. PR #2 can be accepted after the amended implementation and exact-head CI are green.

## Documents

1. [`00-authority-scope-and-precedence.md`](00-authority-scope-and-precedence.md) — authority and hard scope.
2. [`01-g06-migration-ledger.md`](01-g06-migration-ledger.md) — inherited G06 no-regression behavior.
3. [`02-ambiguity-register.md`](02-ambiguity-register.md) — closed MBR-00 decisions.
4. [`03-target-architecture.md`](03-target-architecture.md) — frozen architecture.
5. [`04-ux-state-model.md`](04-ux-state-model.md) — frozen UX state/transition semantics.
6. [`05-gates.md`](05-gates.md) — gates mbr-00 through mbr-10 and current status.
7. [`06-execution-rules.md`](06-execution-rules.md) — execution/evidence discipline.
8. [`07-mbr-00-frozen-contract.md`](07-mbr-00-frozen-contract.md) — gate-level frozen contract.
9. [`08-mbr-00-migration-manifest.md`](08-mbr-00-migration-manifest.md) — G06 reuse/adapt/exclude manifest.
10. [`executions/mbr-00/completion.md`](executions/mbr-00/completion.md) — durable MBR-00 completion report.
11. [`executions/mbr-01/pre-implementation.md`](executions/mbr-01/pre-implementation.md) — exact MBR-01 execution subject before implementation.
12. [`executions/mbr-01/completion.md`](executions/mbr-01/completion.md) — durable MBR-01 implementation/build/CI/integration record.
13. [`executions/mbr-02/pre-implementation.md`](executions/mbr-02/pre-implementation.md) — exact MBR-02 execution subject before implementation.
14. [`executions/mbr-02/blocker.md`](executions/mbr-02/blocker.md) — historical Pair New connected-entry contradiction, now resolved by the 2026-09-20 amendment.
15. [`requirements/2026-09-19-user-rules.md`](requirements/2026-09-19-user-rules.md) — original verbatim UX source.
16. [`requirements/2026-09-20-single-connected-mouse.md`](requirements/2026-09-20-single-connected-mouse.md) — single-live-Mouse simplification.
17. [`requirements/2026-09-20-connected-home-pair-new.md`](requirements/2026-09-20-connected-home-pair-new.md) — connected HOME Pair New entry amendment.
17. [`requirements/2026-09-20-pair-new-help-and-handoff.md`](requirements/2026-09-20-pair-new-help-and-handoff.md) — newest Pair New Help/handoff clarification.

## Gate summary

| Gate | Status | Purpose |
|---|---|---|
| mbr-00 | **COMPLETE / ACCEPTED** | provenance + contract freeze |
| mbr-01 | **COMPLETE / ACCEPTED** | clean bootstrap and architecture guards |
| mbr-02 | **IMPLEMENTED / BLOCKED** | host interaction/state/projector/golden UX; Pair New connected-entry contract unresolved |
| mbr-03 | BLOCKED BY MBR-02 | physical renderer/HAT |
| mbr-04 | PLANNED | fixed USB identity |
| mbr-05 | PLANNED | BLE HOGP passthrough core |
| mbr-06 | PLANNED | G06 profile/persistence/reconnect/HID++ parity |
| mbr-07 | PLANNED | lifecycle/Pair New handoff/registry/removal |
| mbr-08 | PLANNED | complete real UX integration |
| mbr-09 | PLANNED | resilience/regression |
| mbr-10 | PLANNED | final release qualification |

## Evidence boundary

MBR-02 has substantial implemented and CI-validated host-pure behavior, but it is **not accepted** because one required product flow is not reachable from the frozen visible control map. Accepted product `main` therefore remains the MBR-01 baseline until the contradiction is resolved and MBR-02 is revalidated/integrated.
