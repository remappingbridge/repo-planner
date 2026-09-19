# UX state model and layout planning

Status: **PLANNED / LITERAL NORMALIZATION PENDING AMBIGUITIES**.

The verbatim 2026-09-19 source is `requirements/2026-09-19-user-rules.md`. That file is never silently corrected. This document converts it into an implementation-oriented state model while preserving unresolved contradictions in `02-ambiguity-register.md`.

## 1. Inherited interaction rules

Unless explicitly superseded by a final per-screen control map:

- actions execute on release;
- held visible control text becomes white and returns to its resting semantic color on release;
- option selection wraps where Up/Down selection exists;
- selected/current cyan rows are white while selected and return to cyan when selection moves away;
- Help owns all HAT input and `ANY KEY: BACK` returns to its owner;
- lock affects presentation only, not Mouse USB/Bluetooth/remap/reconnect;
- the unlocking interaction is consumed;
- UI reacts immediately to async connection/disconnection/profile-confirmation events; it does not wait for another HAT input;
- screen code sends semantic commands and does not invoke Bluetooth/storage/USB directly.

The old G06 universal `KEY B = one-page Back` rule is inherited only where the new screen contract does not explicitly contradict it. `retry-pair-new` and `escape-active` require explicit decisions before implementation.

## 2. Root state selection

At boot after product-state validation:

```text
saved_mice.empty()
  -> searching-first

saved_mice.not_empty()
  -> home-searching + start bounded saved-search transaction
```

No screen choice is based on stale flash count before persistence integrity/schema validation completes.

## 3. Screen families

### 3.1 No-saved onboarding/search

- `searching-first`
  - root when no saved Mouse exists;
  - starts logically indefinite first-Mouse search made of restartable finite scan/connection cycles;
  - didactic HAT labels provide press/release visual feedback only according to the final control-map decision;
  - never creates a second hidden navigation route.

- `first-mouse-connected`
  - feedback/onboarding after the first successfully persisted+ready Mouse;
  - lifetime/exit semantics are `AMB-014`;
  - must not declare connection before the candidate is classified, secured, persisted and ready.

### 3.2 Saved-device home/search

- `home-searching`
  - root when one or more saved mice exist and a saved-search transaction is active;
  - list entries: Pair New Mouse, Saved Devices, Learn The Keys;
  - async saved connection may transition to connected presentation according to final focus policy;
  - `KEY B: CANCEL SEARCH` must cancel only the search transaction, not remove saved mice or disconnect already-ready sessions.

- `home-searching-help`
  - explains bounded saved search.

- `home-retry`
  - entered when the saved-search window ends without the qualifying result;
  - `KEY A` restarts saved search;
  - list remains usable.

- `home-retry-help`
  - explains that only saved devices were attempted.

### 3.3 Pair New

- `pair-new`
  - starts a bounded transaction restricted to valid Mouse candidates not already accepted as saved under the final `AMB-010` rule;
  - existing ready Mouse sessions continue functioning;
  - cancellation never deletes/replaces an existing saved record.

- `help-pair-new`
  - contextual Help.

- `retry-pair-new`
  - no acceptable new Mouse found in the bounded window;
  - A retries Pair New;
  - B destination/side effect is `AMB-011`.

- `help-retry-pair-new`
  - explains new-only search scope.

### 3.4 Connected home

- `home-connected`
  - shows one UI-focused Mouse name, not the complete runtime connection set;
  - selected Mouse/focus semantics are unresolved in `AMB-002`, `AMB-003`, `AMB-022`;
  - options: focused Mouse remapper, Saved Devices, Learn The Keys;
  - dynamic remapper summary projects the **confirmed** profile kind, never an optimistic draft;
  - profile-label spelling is `AMB-006`/`AMB-020`.

- `help-home-connected`
  - removal directions through Saved Devices;
  - with multi-Mouse, “currently connected Mouse” language must be reconciled with focus semantics before literal freeze.

### 3.5 Remapper

- `remapper-options`
  - options: Passthrough, Default/Standard, Escape, Custom;
  - current confirmed profile gets cyan when unselected, white while selected;
  - Joy Left Back conflicts/overlaps inherited Back policy only if the final control map says so.

- `help-remapper-options`
  - contextual Help.

- `passthrough-not-active` -> Apply request -> runtime/persistence confirmation -> `passthrough-active`.
- already-current Passthrough may enter `passthrough-active` directly, preserving G06 semantics unless explicitly superseded.

- `standard-not-active` -> Apply request -> runtime/persistence confirmation -> `standard-active`.
- already-current Standard may enter `standard-active` directly.

- `escape-not-active` / `escape-active`
  - architecturally present in the supplied UX plan but implementation is blocked by `AMB-001`;
  - `JOY LEFT: GO TO HOME` is separately blocked by `AMB-012`.

- `custom-edit`
  - five dynamic rows from the live Custom draft;
  - per-source edit pages: `left`, `right`, `middle`, `forward`, `backward`;
  - Apply-And-Back updates/persists the draft according to inherited G06 semantics and immediately reprojects the parent row;
  - `APPLY CUSTOM` only produces success/current state after runtime + persistence confirmation;
  - Escape target remains blocked by `AMB-001`.

The new source does not define a separate `CUSTOM APPLIED` page. Therefore the old G06 success-page behavior is not automatically inserted; the final Custom success presentation must follow the new explicit screen set or an explicit decision.

### 3.6 Saved Devices

- `saved-devices`
  - one page per saved Mouse in the supplied design, with title `N OF M`;
  - name is dynamic;
  - `STATUS` derives independently per Mouse from the live session set;
  - `PROFILE` derives from that Mouse's confirmed saved profile kind;
  - Left/Right page navigation wraps only if explicitly retained by final control map;
  - `REMOVE DEVICE` is the selectable action.

This is intentionally different from G06's “up to four saved devices per page” layout. The new one-Mouse-per-page layout supersedes it.

- `remove-this`
  - shows the selected saved Mouse name;
  - A requests transactional removal;
  - B cancels/back according to final transition table;
  - removal does not visually complete until source release/disconnect/product-state/credential cleanup reaches the gate-defined commit point;
  - if the removed Mouse is the last saved record, successful removal leads to `searching-first` and first-pair search;
  - otherwise successful removal returns to `saved-devices` on a valid remaining page.

- `help-remove-this`
  - explains loss of automatic reconnect and remap profile.

### 3.7 Learn

- `learn-the-keys`
  - never boot root under the new contract;
  - displays the HAT didactic map;
  - literal title/coordinates are blocked by `AMB-007`/`AMB-008`;
  - no Bluetooth/remap side effects from didactic presses;
  - exact Key Y lock/open-home semantics must be frozen because the new layout text and inherited Learn behavior are not fully aligned.

## 4. Async transitions

Screens must subscribe to semantic application events rather than poll incidental input.

Examples:

```text
MouseReady(mouse_id)
MouseDisconnected(mouse_id, reason)
SavedSearchExpired(transaction_id)
PairNewExpired(transaction_id)
ProfileApplyConfirmed(mouse_id, profile)
ProfileApplyFailed(mouse_id, reason)
RemoveConfirmed(mouse_id)
RemoveFailed(mouse_id, reason)
PersistenceRecovered(previous_generation)
```

Every event includes enough transaction/session identity to ignore stale completion from an operation that has already been canceled/replaced.

## 5. Multi-Mouse UI invariant

A UI focus Mouse, once defined, is only a presentation/edit target. It never means:

- disconnect every other Mouse;
- suppress input from every other Mouse;
- make other connected mice report `SAVED` instead of `CONNECTED`;
- allow profile actions for one Mouse to mutate another Mouse's profile kind;
- change source ownership identity.

## 6. Layout geometry

Starting physical renderer baseline inherited from accepted G06:

- LCD 240x240;
- 5x7 glyph source at scale 2;
- glyph box 10x14;
- x origin 7;
- horizontal advance 11 px;
- title y 8;
- standard first body y 39;
- standard body advance 26 px;
- final standard hint y 214, bottom anchored;
- dark-magenta hint region begins 11 px above first visible hint;
- didactic Learn-style body used the accepted special row spacing in G06.

The new source changes horizontal character coordinates for didactic layouts. Those explicit new coordinates take precedence once normalized, but physical vertical relocation is retained unless explicitly changed.

## 7. Dynamic text policy to freeze before renderer gate

The final UX contract must define:

- Mouse-name truncation/ellipsis/scroll behavior;
- exact profile display strings (`DEFAULT` vs `STANDARD`);
- exact disconnected status text;
- max `N OF M` representation that fits 21 columns;
- behavior when a persisted Mouse has no readable name (address-derived fallback vs generic name);
- capitalization and typo corrections listed in `AMB-020`.

No renderer should guess these based on available pixels.

## 8. Golden layout tests

Before physical HAT acceptance, host tests must project every screen with representative states and assert:

- 9-row/21-column constraints where retained;
- exact frozen literal rows;
- dynamic field substitution without leftover annotation text such as `(nome do mouse)` or `(customizável)`;
- exact token start columns for didactic screens;
- selection/current/pressed color precedence;
- hint count/anchoring;
- hidden-control map separate from visible text;
- no `Keyboard`/`Composite` product pages;
- no accidental old `HOME`/`OTHER OPTIONS` path unless explicitly reintroduced by a resolved rule;
- all declared screen IDs reachable only through permitted transitions.

## 9. Required mbr-00 output for UX

Before `mbr-02`/`mbr-03` can be accepted, mbr-00 must produce one canonical screen table derived from the verbatim requirement source with:

```text
screen_id
literal/dynamic rows 0..8
field formatting rules
selectable rows/order
visible controls
hidden controls
press feedback
release actions
async transitions
Back target
lock policy
focus-Mouse requirement
source ambiguity decisions
```

That table, not informal resemblance to the old BLU2USB UI, becomes the executable UX specification.
