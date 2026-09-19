# Mouse Bridge Remapper planning

Status: **PLANNED ONLY. No `mbr-*` gate has been executed.**

This directory is the planning source of truth for `tiagooliveirajs/mouse-bridge-remapper`.

## Goal

Rebuild the Mouse portion of the physically accepted BLU2USB product through G06, then adapt it to the Mouse Bridge Remapper product and its new UX.

The product may keep multiple mice saved, but **only one mouse may be connected at a time**. Keyboard pairing and Composite devices are intentionally outside scope. Escape remains as synthetic USB Keyboard output generated only by mouse remapping.

Accepted migration reference:

- `tiagooliveirajs/blu2usb`
- branch `gate/g06-profiles-remap-logitech-hidpp`
- physically accepted SHA `7eee024ad4ee726c5a85ffa2f32b9f47187878af`
- PR #8 physical acceptance

Do not use G07+ Keyboard branches as a production base.

## Current product decisions

The 2026-09-20 simplification supersedes earlier simultaneous-mouse planning:

- zero or one live mouse connection;
- multiple saved mice remain supported;
- `home-connected` always refers to the one connected mouse;
- HOME with saved mice and no live connection enters `home-searching` and automatically starts bounded saved-device search;
- if the saved search expires, HOME becomes `DEVICE NOT FOUND`;
- if the connected mouse powers off/disconnects, the same HOME/saved-search flow starts automatically;
- `PAIR NEW MOUSE` first releases/disconnects the current live mouse, keeps it saved, then searches for one unsaved replacement;
- one discovery transaction accepts only one winner;
- there is no simultaneous-Mouse capacity requirement, `N DEVICES CONNECTED` UI, multi-Mouse focus model or cross-mouse HID aggregation.

## Documents

1. [`00-authority-scope-and-precedence.md`](00-authority-scope-and-precedence.md) — authority order, scope and exact G06 baseline.
2. [`01-g06-migration-ledger.md`](01-g06-migration-ledger.md) — preserve/adapt/exclude ledger and historical bug lessons.
3. [`02-ambiguity-register.md`](02-ambiguity-register.md) — remaining contradictions/incomplete rules that implementation may not resolve silently.
4. [`03-target-architecture.md`](03-target-architecture.md) — clean single-live-session architecture, persistence, BLE, USB and UI boundaries.
5. [`04-ux-state-model.md`](04-ux-state-model.md) — implementation-oriented state/transition/layout model.
6. [`05-gates.md`](05-gates.md) — planned sequence `mbr-00` through `mbr-10`.
7. [`06-execution-rules.md`](06-execution-rules.md) — branch, evidence, regression, UF2 and physical-acceptance discipline.
8. [`requirements/2026-09-19-user-rules.md`](requirements/2026-09-19-user-rules.md) — original supplied layout/rules preserved as provenance.
9. [`requirements/2026-09-20-single-connected-mouse.md`](requirements/2026-09-20-single-connected-mouse.md) — current simplification that supersedes simultaneous-mouse rules.

## Remaining contract decisions

The major simultaneous-mouse ambiguities are closed by design and must not be reintroduced.

Remaining decisions include:

- exact final USB VID/PID/manufacturer/product strings;
- final visible naming `DEFAULT REMAP` vs `STANDARD REMAP`;
- exact saved-search and Pair New timeout constants;
- exact long-mouse-name rendering policy;
- final control semantics for `FIRST MOUSE CONNECTED`;
- exact one-step navigation effect of `KEY B: BACK TRY SAVED`;
- exact disconnected status word in Saved Devices;
- any unresolved literal/coordinate/navigation issue retained in `02-ambiguity-register.md`.

Escape is no longer an architecture blocker: the product explicitly permits a minimal fixed USB Keyboard output capability solely for synthetic Escape while continuing to prohibit Bluetooth Keyboard/Composite pairing.

## Gate summary

| Gate | Purpose |
|---|---|
| mbr-00 | provenance + remaining decisions + canonical contract freeze |
| mbr-01 | clean bootstrap and architecture guards |
| mbr-02 | host interaction/state/projector and golden layouts |
| mbr-03 | physical Waveshare renderer/HAT acceptance |
| mbr-04 | fixed USB Mouse + synthetic Escape identity/output |
| mbr-05 | canonical single-session Mouse core + BLE HOGP passthrough |
| mbr-06 | accepted G06 profiles/persistence/reconnect/HID++ migration |
| mbr-07 | saved/new search, single-connection replacement, registry and removal |
| mbr-08 | complete new UX integration |
| mbr-09 | resilience/regression qualification |
| mbr-10 | final physical release qualification |

## Planning boundary

Updating these documents does not execute `mbr-00`, build firmware, flash a Pico, produce an accepted UF2 or make a physical acceptance claim.
