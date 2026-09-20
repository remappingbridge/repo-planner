# MBR-02 contract blocker — Pair New connected entry path

Gate: `mbr-02 — Interaction engine, UI projector and golden screen model`  
Status: **IMPLEMENTED / ACCEPTANCE BLOCKED**

## Finding

During executable transition modeling, the frozen MBR-00 product contract exposed a reachability contradiction that cannot be resolved in code without silently changing the documented UX.

The product contract requires a supported case in which `PAIR NEW` begins while one Mouse is already authoritative/connected and remains usable during discovery. However, the canonical screen/control inventory exposes `PAIR NEW MOUSE` only on:

- `home-searching`, whose precondition is saved records + **no authoritative Mouse**; and
- `home-retry`, which follows saved-search expiry/cancel in the same no-live HOME family.

The canonical `home-connected` screen contains only:

1. the current Mouse profile/remap summary;
2. `SAVED DEVICES`;
3. `LEARN THE KEYS`;
4. `KEY X: HELP TO REMOVE`.

It contains no visible Pair New action. The same contract explicitly forbids inheriting hidden controls.

Therefore there is currently no documented user-reachable transition from a valid `saved + live Mouse -> home-connected` state into `pair-new` while preserving that live Mouse.

## Why implementation did not guess

Any of these would be an unauthorized product change:

- add `PAIR NEW MOUSE` to `home-connected`;
- replace one existing `home-connected` row/option with Pair New;
- assign Pair New to a hidden Key/control;
- allow SEARCH_SAVED to keep running concurrently after entering Pair New so that a Mouse later becomes live;
- reinterpret the profile-summary row as Pair New.

The execution rules require stopping dependent work when the frozen documentation is materially contradictory rather than choosing an interpretation silently.

## Work completed despite blocker

Implementation branch:

`tiagooliveirajs/mouse-bridge-remapper:mbr/mbr-02-host-ux-model`

Current exact implementation head at blocker discovery:

`13cad3fec43eeed0353b0271a018012d115f2845`

PR:

`#2 — MBR-02: host UX model and golden screen contract`

Implemented and host-tested:

- all 30 canonical screen IDs/literals;
- 21-character name projection/fallback;
- release-triggered interaction;
- Help ownership/consumption;
- ordinary and instructional Lock semantics;
- HOME resolver;
- FIRST_MOUSE 8s repeated transaction model;
- SEARCH_SAVED 8s transaction model;
- PAIR_NEW 15s semantic transaction model;
- transaction/session stale-event filtering;
- one authoritative Mouse + one non-authoritative Pair New candidate representation;
- saved-candidate rejection for PAIR_NEW;
- Pair New timeout/cancel/handoff semantics at the state-machine API level;
- STANDARD vocabulary;
- Saved Devices connected/disconnected projection and cyan priority;
- Custom draft immediate projection;
- confirmation-only profile/removal success;
- exact Pair New Help literals and didactic columns;
- Escape active HOME shortcut;
- no Keyboard/Composite/multi-connected screen state.

The host CI at exact SHA `13cad3fec43eeed0353b0271a018012d115f2845` passed `bootstrap_contract`, `ux_golden_contract`, `ux_behavior_contract`, and `architecture_contract`.

The behavior test can exercise the required Pair New-with-live semantics by constructing that semantic state directly. This proves the state model supports it, but **does not prove the user can reach it through the frozen control map**. That distinction is the blocker.

## Additional implementation finding

When MBR-02 replaced MBR-01 stubs with real dependent static libraries, the MBR-01 CMake facade exposed a latent static-library link-order problem. The module graph itself was preserved, but declaration/wiring had to be separated so each implementation library precedes its dependencies on the final link line. Host build/tests pass after this correction.

## Acceptance impact

MBR-02 must not be marked `COMPLETE / ACCEPTED` and PR #2 must not be merged as the accepted gate baseline while the Pair New connected-entry contradiction remains unresolved.

No later gate may be started from this blocked state.

## Required product decision

The documentation must explicitly define a visible, reachable way to start `PAIR NEW` while `home-connected` has a live Mouse, or explicitly change the product requirement so Pair New is not available while a Mouse is live.

After that decision is documented, MBR-02 must update the canonical screen/control transition model and golden tests, rerun exact-head CI, integrate the PR, and record a completion report before mbr-03 can begin.
