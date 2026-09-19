# Ambiguity and decision register

Status: **OPEN PLANNING REGISTER**. No item in this file is permission to change firmware yet.

The purpose of this register is to prevent implementation from resolving contradictory or incomplete product rules opportunistically. `mbr-00` must freeze the required decisions before any dependent implementation gate can close.

Severity:

- **BLOCKER** — dependent implementation cannot be accepted until explicitly resolved.
- **MATERIAL** — architecture can proceed behind an abstraction, but the affected UX/behavior gate cannot close.
- **EDITORIAL** — literal contract still needs normalization/golden tests, but the architecture is not blocked.

## AMB-001 — Mouse-only USB scope vs Escape remapping

**Severity:** BLOCKER.

The project is explicitly Mouse-only and excludes Keyboard and Composite. The supplied new screens still contain `ESCAPE REMAP`, `LEFT IS ESCAPE` and Custom targets named `ESCAPE`.

BLU2USB G06 implemented Escape by keeping a firmware-owned **USB Keyboard interface** beside the USB Mouse interface. A standard USB HID Mouse interface cannot emit the Keyboard Escape key.

Possible product decisions are mutually exclusive:

1. remove Escape as a profile/Custom target and remain strictly one USB HID Mouse function;
2. permit a fixed synthetic-keyboard USB interface solely for remap output, which technically makes the USB device composite Mouse+Keyboard even though no physical Keyboard pairing exists;
3. redefine what `ESCAPE` means to something other than a standard Keyboard Escape key, which would be a new product behavior and must be explicitly specified.

**Planning treatment:** target architecture remains Mouse-only; no Keyboard/Composite implementation is introduced. Gates touching USB identity or Escape behavior remain blocked until this is decided.

## AMB-002 — Multiple connected mice vs singular `home-connected`

**Severity:** BLOCKER for profile UX.

General rules say several mice may be connected simultaneously. `home-connected` displays exactly one Mouse name and describes it as “o dispositivo atualmente conectado”. Its first option opens that Mouse's remapper options.

Undefined: when two or more mice are connected, which Mouse is displayed and receives profile edits?

**Architecture treatment:** separate the runtime `connected_mouse_ids` set from an optional UI-only `focused_mouse_id`. Do not let a single “active mouse” object become transport truth again. The rule that chooses/changes focus remains open.

## AMB-003 — How a user selects another connected Mouse for remapping

**Severity:** BLOCKER for multi-Mouse profile UX.

No supplied screen offers an explicit “choose connected mouse for remapping” action. `saved-devices` pages expose only `REMOVE DEVICE`, while `home-connected` exposes the profile of one Mouse.

Undefined possibilities include page navigation choosing focus, last-connected wins, last-input wins, or a new explicit action. None may be invented silently.

## AMB-004 — Boot reconnect with several saved mice

**Severity:** BLOCKER for final reconnect semantics.

`home-searching` says it searches a saved Mouse for a finite time. The global rule allows multiple connected mice. Undefined: should boot stop after the first saved Mouse reconnects, or continue attempting all saved mice up to the supported simultaneous limit?

**Architecture treatment:** implement reconnect scheduling as policy over independent sessions. BLE parsing must not encode first-only/all behavior.

## AMB-005 — Maximum simultaneous and saved Mouse count

**Severity:** MATERIAL.

“Vários mouses” requires more than one but specifies no maximum. RP2350/BTstack memory, HIDS client contexts and peripheral behavior impose a finite practical limit.

**Planning treatment:** no arbitrary user-visible maximum is frozen now. `mbr-07` is a physical feasibility/capacity gate. It must prove at least two simultaneous BLE HOGP mice and determine the supportable production maximum before release qualification. Saved-device capacity may be larger and is separately bounded by persistent storage/UI pagination.

## AMB-006 — `DEFAULT REMAP` vs `STANDARD REMAP`

**Severity:** MATERIAL/EDITORIAL.

The new `MOUSE OPTIONS` layout says `DEFAULT REMAP`, matching accepted G06. `home-connected` dynamic-label rules call the same profile `STANDARD REMAP` and display `REMAPPED TO STANDARD`; the dedicated pages are named `STANDARD REMAP`.

Undefined: canonical internal/profile name and exact visible wording.

**Planning treatment:** runtime uses an internal stable profile identifier independent from display strings. Literal display wording must be frozen before the UX gate.

## AMB-007 — Learn/search-first title text conflicts inside the new rules

**Severity:** MATERIAL/EDITORIAL.

The displayed block says `PRESS TO LEARN KEYS`; a rule refers to `PRESS A KEY TO LEARN`; accepted G06 used `PRESS TO LEARN A KEY`.

Because the supplied file both replaces the old layout and contradicts itself, no variant is silently preferred.

## AMB-008 — New character coordinates vs old accepted coordinates

**Severity:** MATERIAL/EDITORIAL.

The new Learn/search-first layouts explicitly move several tokens compared with G06 (for example three `JOY` labels begin at columns 3/10/17 instead of old 1/8/15). These explicit new coordinates are intended to supersede old positions, but some wording says “linha 16” where the context clearly appears to discuss a column and some headings say “na coluna” for a row.

**Planning treatment:** preserve the supplied coordinate declarations verbatim, then freeze corrected coordinate assertions as an explicit mbr-00 decision before rendering implementation. Never infer positions from visual spacing alone.

## AMB-009 — `searching-first` vs `searching-first-mouse`

**Severity:** EDITORIAL.

The screen is named `searching-first`, while several flows/rules call it `searching-first-mouse`.

**Architecture treatment:** one internal screen ID only; exact canonical name must be normalized in mbr-00 and all aliases documented.

## AMB-010 — Incomplete Pair New rule

**Severity:** MATERIAL.

The rule ends mid-sentence: “caso o mouse esteja na lista”. The intent appears to be that `pair-new` accepts only a Mouse not already saved, but the missing consequence is not specified.

**Architecture treatment:** candidate classification can distinguish already-saved vs new without deleting or overwriting anything. The UX behavior after discovering a saved peer while `pair-new` is active remains open.

## AMB-011 — Pair/Retry Back destinations

**Severity:** MATERIAL.

Accepted G06 had a global rule: `KEY B` means exactly one logical page Back. The new `retry-pair-new` label says `KEY B: BACK TRY SAVED`, suggesting a semantic transition that may also start a saved-device search. `pair-new` says `CANCEL` but does not state the precise destination/side effect.

**Planning treatment:** preserve release-triggered Back semantics and model navigation separately from search commands. Exact transition table must be frozen before mbr-08/mbr-09.

## AMB-012 — `JOY LEFT: GO TO HOME` conflicts with inherited G06 rule

**Severity:** BLOCKER for that screen.

Accepted G06 explicitly removed all `GO TO HOME` behavior and defined Back as one logical page. The new `escape-active` screen explicitly prints `JOY LEFT: GO TO HOME`.

Because this is an explicit new rule, it would normally supersede G06, but it appears only on one profile feedback screen and conflicts with the otherwise inherited navigation model. It must be confirmed as intentional rather than treated as a typo.

## AMB-013 — Lock availability on replaced screens

**Severity:** MATERIAL.

Some new screens print `KEY Y: LOCK`, some do not, and Escape screens differ from Passthrough/Standard. Accepted G06 also had hidden Key Y Lock on some pages.

Undefined: does omission in the new replacement layout intentionally remove lock, or are old hidden controls inherited?

**Planning treatment:** every screen gets an explicit control map in the final transition table; no hidden control is inferred merely because an old screen with similar purpose had one.

## AMB-014 — `first-mouse-connected` lifetime and exit behavior

**Severity:** MATERIAL.

The screen must appear after the very first Mouse connects, but the rules do not state how/when it transitions to `home-connected`, whether all didactic keys are inert like Learn, or which action opens home.

The visible text suggests Key Y is associated with `OPEN HOME`, while old Learn semantics associated Key Y with lock. This needs an explicit transition/control map.

## AMB-015 — Saved-device status/profile for multiple live mice

**Severity:** MATERIAL.

Per-Mouse saved pages show `STATUS: CONNECTED` and `PROFILE: STANDARD`, which naturally supports several pages showing CONNECTED at once. Exact values for disconnected/saved states and canonical profile strings are not exhaustively specified.

**Architecture treatment:** projection derives from `SavedMouse + optional live session + confirmed profile`. Do not collapse status to one global active Mouse.

## AMB-016 — Removing a connected Mouse while others remain

**Severity:** MATERIAL.

Rules specify destination based on whether it is the only **saved** Mouse, but do not explicitly say how a live connected session is disconnected/released before credentials/profile data are erased.

**Inherited safety treatment:** removal must be transactional and release only that Mouse's source ownership. Other connected mice continue uninterrupted. The exact UI timing/error presentation if disconnect/credential deletion fails remains open.

## AMB-017 — Long Mouse names and 21-character layout

**Severity:** MATERIAL/EDITORIAL.

The new layouts use dynamic Mouse names, but no truncation, scrolling, wrapping or fallback-name policy is specified. G06 renderer contracts used a 21-character semantic width.

**Planning treatment:** storage keeps the full normalized available name; projection needs a deterministic display policy frozen before renderer acceptance.

## AMB-018 — New USB VID/PID/manufacturer/product strings

**Severity:** BLOCKER for USB identity acceptance.

G06 used VID `0xCAFE`, PID `0x4010`, manufacturer `BLU2USB`, product `BLU2USB Mouse + Keyboard`, and two HID interfaces. The new project name/scope makes that literal identity misleading and the Keyboard interface conflicts with Mouse-only scope.

**Architecture treatment:** identity remains fixed from boot and never Bluetooth-driven, but exact descriptor identity must be explicitly frozen in mbr-00/mbr-04.

## AMB-019 — Exact BLE Mouse transport scope

**Severity:** MATERIAL.

Accepted G06 Mouse transport is BLE HOGP. The new wording says Mouse generically but does not explicitly request Bluetooth Classic Mouse support.

**Planning default:** BLE HOGP is the only planned production Mouse transport because it is the accepted baseline. Classic HID Mouse is out of scope unless explicitly added later; this is an assumption to be ratified, not an implicit feature claim.

## AMB-020 — `FORWARED`, `T0`, capitalization and metadata typos

**Severity:** EDITORIAL.

Known literal anomalies in the supplied file include:

- `REMAPPED T0 ESCAPE` (`T0` with zero);
- `MIDDLE IS FORWARED`;
- lower-case `kEY Y`;
- `Titulo da tela:` containing what appears to be a flow;
- blank `Fluxo até a tela` fields;
- stray backticks in names/flows;
- “DEFAULT OPTIONS” where singular may have been intended.

These are preserved in the verbatim requirement file. No renderer should implement corrected wording until the canonical literal table is frozen.

## AMB-021 — Search timeout values

**Severity:** MATERIAL.

No-saved first search is logically indefinite/very long. Saved search and Pair New are finite but no duration is specified. G06 used an 8-second bound for preferred bonded reconnect, which is evidence but not automatically the new UX timeout.

**Architecture treatment:** expose timing as coordinator policy constants with host-testable state transitions. Exact values are gate decisions, not BLE-parser constants.

## AMB-022 — What happens when a new Mouse connects while others are already connected

**Severity:** BLOCKER for full multi-Mouse UX.

Undefined whether `home-connected` should immediately focus the newly connected Mouse, preserve the prior focus, or indicate several connections another way. A connection event must never disconnect existing mice merely to simplify the UI.

## AMB-023 — Global CustomTemplate effect on several Custom-profile mice

**Severity:** MATERIAL.

G06 defines one global CustomTemplate. With several connected mice whose profile kind is Custom, committing a changed global template would logically change all of them. The new rules do not say whether that is desired.

**Planning inherited default:** keep the G06 global template model until explicitly superseded, but mbr-09 must not close without testing and documenting the multi-Mouse consequence. If independent per-Mouse custom mappings are wanted, that is a schema/product change.

## AMB-024 — First-search control behavior vs color text mismatch

**Severity:** MATERIAL/EDITORIAL.

`searching-first` says no key navigates and only the corresponding label changes white while held, but its rule names `PRESS A KEY TO LEARN` while the screen text is `PRESS TO LEARN KEYS`. It also does not explicitly state whether Key Y can lock on this screen.

**Planning treatment:** treat searching-first as its own didactic+search state, not automatically the old Learn screen. Explicit control map required.

## Decision discipline

- An ambiguity may be resolved only by an explicit planning edit recording the chosen behavior and affected gates.
- Typographical cleanup that changes literal displayed text is still a decision because screen text is part of the acceptance contract.
- Implementation may create abstractions that keep options open, but may not claim a blocked feature accepted.
- When a decision changes a previously inherited G06 behavior, update the migration ledger and regression matrix in the same planning change.
