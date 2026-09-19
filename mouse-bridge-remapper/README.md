# Mouse Bridge Remapper planning

Status: **MBR-00 COMPLETE / ACCEPTED. NEXT GATE: MBR-01.**

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
11. [`requirements/2026-09-19-user-rules.md`](requirements/2026-09-19-user-rules.md) — original verbatim UX source.
12. [`requirements/2026-09-20-single-connected-mouse.md`](requirements/2026-09-20-single-connected-mouse.md) — single-live-Mouse simplification.
13. [`requirements/2026-09-20-pair-new-help-and-handoff.md`](requirements/2026-09-20-pair-new-help-and-handoff.md) — newest Pair New Help/handoff clarification.

## Gate summary

| Gate | Status | Purpose |
|---|---|---|
| mbr-00 | **COMPLETE** | provenance + contract freeze |
| mbr-01 | NEXT / PLANNED | clean bootstrap and architecture guards |
| mbr-02 | PLANNED | host interaction/state/projector/golden UX |
| mbr-03 | PLANNED | physical renderer/HAT |
| mbr-04 | PLANNED | fixed USB identity |
| mbr-05 | PLANNED | BLE HOGP passthrough core |
| mbr-06 | PLANNED | G06 profile/persistence/reconnect/HID++ parity |
| mbr-07 | PLANNED | lifecycle/Pair New handoff/registry/removal |
| mbr-08 | PLANNED | complete real UX integration |
| mbr-09 | PLANNED | resilience/regression |
| mbr-10 | PLANNED | final release qualification |

## MBR-00 evidence boundary

MBR-00 changed documentation/planning only. It did **not** implement firmware, build code, produce a UF2, flash hardware or make a new physical acceptance claim.
