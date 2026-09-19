# Mouse Bridge Remapper planning

Status: **PLANNED ONLY. No `mbr-*` gate has been executed.**

This directory is the planning source of truth for `tiagooliveirajs/mouse-bridge-remapper`.

## Goal

Rebuild the Mouse portion of the physically accepted BLU2USB product through G06, then adapt it to a Mouse-only product with the new 2026-09-19 UX and **multiple simultaneously connected mice**. Keyboard pairing and Composite devices are intentionally outside scope.

Accepted migration reference:

- `tiagooliveirajs/blu2usb`
- branch `gate/g06-profiles-remap-logitech-hidpp`
- physically accepted SHA `7eee024ad4ee726c5a85ffa2f32b9f47187878af`
- PR #8 physical acceptance

Do not use G07+ Keyboard branches as a production base.

## Documents

1. [`00-authority-scope-and-precedence.md`](00-authority-scope-and-precedence.md) — authority order, Mouse-only scope, exact G06 baseline and exclusions.
2. [`01-g06-migration-ledger.md`](01-g06-migration-ledger.md) — preserve/adapt/exclude ledger and historical bug lessons that must become regressions.
3. [`02-ambiguity-register.md`](02-ambiguity-register.md) — contradictions and incomplete rules that implementation may not resolve silently.
4. [`03-target-architecture.md`](03-target-architecture.md) — from-zero multi-Mouse architecture, ownership, persistence, BLE, USB and UI boundaries.
5. [`04-ux-state-model.md`](04-ux-state-model.md) — implementation-oriented state/transition/layout model derived from the new UX.
6. [`05-gates.md`](05-gates.md) — planned sequence `mbr-00` through `mbr-11`.
7. [`06-execution-rules.md`](06-execution-rules.md) — branch, evidence, regression, UF2 and physical-acceptance discipline.
8. [`requirements/2026-09-19-user-rules.md`](requirements/2026-09-19-user-rules.md) — verbatim source rules/layout supplied by the user; preserved without silent typo correction.

## Important unresolved contract conflicts

The complete list is in the ambiguity register. The most consequential are:

- **Escape vs Mouse-only USB:** the new rules retain `ESCAPE REMAP`, but a standard USB Mouse interface cannot emit Keyboard Escape. BLU2USB G06 solved this with a fixed USB Keyboard interface, which conflicts with the new “no Keyboard / no Composite” scope.
- **Several connected mice vs one `home-connected` Mouse:** the runtime requirement is multi-Mouse, but the supplied profile UX shows one Mouse name and does not yet define how the user chooses which connected Mouse is being edited.
- **Saved reconnect policy:** it is not yet defined whether boot reconnects one qualifying saved Mouse or continues connecting several saved mice.
- **Literal UX conflicts:** `DEFAULT` vs `STANDARD`, several Learn/search title variants and coordinates, `JOY LEFT: GO TO HOME` vs inherited one-page Back, and incomplete Pair New behavior need explicit normalization.
- **USB identity:** the old BLU2USB Mouse+Keyboard VID/PID/product contract cannot be copied literally into a strict Mouse-only product without a deliberate decision.

These are not reasons to discard the plan. The architecture deliberately isolates them behind policy/UX/output boundaries, and `mbr-00` is the gate that freezes the choices before implementation.

## Gate summary

| Gate | Purpose |
|---|---|
| mbr-00 | provenance + ambiguity decisions + canonical contract |
| mbr-01 | clean Mouse-only bootstrap and architecture guards |
| mbr-02 | host interaction/state/projector and golden layouts |
| mbr-03 | physical Waveshare renderer/HAT acceptance |
| mbr-04 | fixed host USB identity |
| mbr-05 | canonical Mouse core + one BLE HOGP passthrough |
| mbr-06 | accepted G06 Mouse profiles/persistence/reconnect/HID++ migration |
| mbr-07 | simultaneous multi-Mouse feasibility and qualification |
| mbr-08 | saved/new search, registry, reconnect and removal |
| mbr-09 | complete new UX and per-Mouse profile targeting |
| mbr-10 | resilience/regression qualification |
| mbr-11 | final physical release qualification |

## Planning boundary

Creating/updating these documents does not start `mbr-00`, create a firmware implementation branch, build firmware, flash a Pico, produce an accepted UF2 or make any physical acceptance claim.
