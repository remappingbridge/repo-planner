# UX state model and layout planning

Status: **PLANNED / CANONICAL BEHAVIOR WITH SOME LITERAL DECISIONS STILL OPEN**.

The current product authority is the combination of:

- `requirements/2026-09-19-user-rules.md` for original screen/layout intent;
- `requirements/2026-09-20-single-connected-mouse.md` for the later simplification;
- current user-facing documentation in `tiagooliveirajs/mouse-bridge-remapper`.

The 2026-09-20 decision supersedes every earlier simultaneous-Mouse rule.

## 1. Global interaction rules

Unless a final per-screen control map explicitly says otherwise:

- actions execute on release;
- visible held control text becomes white and returns to its resting semantic color on release;
- selected/current cyan rows are white while selected and return to cyan when selection moves away;
- Help owns all HAT input and `ANY KEY: BACK` returns to its owner;
- lock affects presentation only, not Mouse USB/Bluetooth/remap/search;
- the unlocking interaction is consumed;
- UI reacts immediately to semantic async connection/disconnection/profile/search events;
- screen code emits semantic commands and never calls Bluetooth/storage/USB directly.

## 2. Single HOME resolver

HOME is not a collection of unrelated entry rules. It is resolved from saved state and the single live connection slot:

```text
if saved_mice.empty():
  searching-first
  ensure FIRST_MOUSE search active

else if live_mouse.ready():
  home-connected
  no saved-search transaction

else:
  home-searching
  start bounded SEARCH_SAVED transaction automatically
```

This same resolver is used for:

- boot;
- returning to HOME from another screen;
- leaving Learn through its HOME action;
- unlocking when the destination is HOME;
- live Mouse disconnect/power-off;
- return from failed/canceled Pair New when no new Mouse connected.

`SEARCH_SAVED` accepts the first saved Mouse that becomes ready and stops. If it expires, UI transitions to `home-retry` / `DEVICE NOT FOUND`.

## 3. Screen families

### 3.1 No-saved onboarding/search

#### `searching-first`

- root when no saved Mouse exists;
- starts logically indefinite first-Mouse search using restartable finite BLE cycles;
- accepts exactly one first valid Mouse;
- didactic HAT labels provide visual press feedback only according to final control table;
- no hidden navigation route.

#### `first-mouse-connected`

- appears only after the first Mouse is authenticated/classified/persisted/ready;
- exact lifetime/control semantics remain `AMB-010`;
- after it exits to HOME, HOME resolves to `home-connected` because the first Mouse is live.

### 3.2 Saved-device HOME search

#### `home-searching`

Precondition:

- one or more saved mice;
- no live Mouse.

Entry side effect:

- automatically start a bounded `SEARCH_SAVED` transaction.

Visible options:

- Pair New Mouse;
- Saved Devices;
- Learn The Keys.

`KEY B: CANCEL SEARCH` cancels only that search transaction; it does not delete saved records.

If the connected Mouse powers off while `home-connected` is visible, the runtime publishes disconnect, clears the live slot, resolves HOME and enters this screen automatically with a fresh saved search.

#### `home-searching-help`

Explains the bounded saved search and that accessing the screen triggers it.

#### `home-retry`

Entered only after the current saved-search window expires without a ready Mouse.

`KEY A: RETRY SEARCH` starts a fresh saved search and returns to `home-searching`.

#### `home-retry-help`

Explains that only saved devices were searched.

### 3.3 Pair New — replacement transaction

#### `pair-new`

Entering Pair New means the user explicitly wants one unsaved Mouse.

If a Mouse is currently connected, application behavior before/while entering Pair New is:

```text
stop accepting current-session events
 -> release held Mouse/Escape output
 -> disconnect current Mouse
 -> clear live slot
 -> keep SavedMouse record + bond
 -> start PAIR_NEW search
```

Pair New then:

- accepts only an unsaved valid Mouse;
- accepts at most one winner;
- stops immediately after the first accepted new Mouse becomes ready;
- never leaves the old and new Mouse simultaneously ready.

If Pair New fails/cancels, the old Mouse remains saved but disconnected. Pair New itself does not silently reconnect it.

#### `help-pair-new`

Contextual Help.

#### `retry-pair-new`

Displayed after bounded Pair New expires without an acceptable unsaved Mouse.

- A retries Pair New;
- B exact one-step transition remains `AMB-007`, but once navigation reaches HOME with no live Mouse the standard HOME resolver automatically starts saved search;
- X Help;
- Y Lock if confirmed by final control map.

#### `help-retry-pair-new`

Explains new-only search scope.

### 3.4 Connected HOME

#### `home-connected`

Precondition: exactly one live Mouse.

- line 2 shows that Mouse name;
- first option shows the **confirmed** profile summary for that same Mouse;
- first option opens remapper options for that same Mouse;
- Saved Devices and Learn remain available.

There is no connected-device count form, focus Mouse or multi-Mouse profile target.

If the live Mouse disconnects, this screen leaves immediately through the HOME resolver and becomes `home-searching` while saved search starts.

#### `help-home-connected`

Removal directions refer to the one currently connected Mouse.

### 3.5 Remapper

#### `remapper-options`

Options:

- Passthrough;
- Default/Standard;
- Escape;
- Custom.

All actions target the single currently connected Mouse.

Current confirmed profile is cyan when unselected and white when selected.

#### Profile detail/apply pages

- `passthrough-not-active` -> confirmed Apply -> `passthrough-active`;
- `standard-not-active` -> confirmed Apply -> `standard-active`;
- `escape-not-active` -> confirmed Apply -> `escape-active`;
- current profile may enter the active page directly.

No profile page may claim success before runtime + persistence confirmation.

`JOY LEFT: GO TO HOME` on `escape-active` remains `AMB-008` until mbr-00 freezes the final navigation contract.

#### `custom-edit`

Five dynamic draft rows.

Per-source pages:

- `left`;
- `right`;
- `middle`;
- `forward`;
- `backward`.

Allowed targets include Escape.

Apply-and-Back updates the draft immediately; full Apply becomes active only after runtime + persistence confirmation.

### 3.6 Saved Devices

#### `saved-devices`

- one saved Mouse per page;
- title `N OF M`;
- Mouse name line;
- status;
- confirmed profile;
- Remove Device.

Connection projection:

- if this page is the single live Mouse, name line is cyan and status is connected;
- all other saved Mouse pages are disconnected/non-cyan;
- at most one page can be connected/cyan.

Exact disconnected status word remains `AMB-011`.

#### `remove-this`

If the target is currently live:

1. stop new session events;
2. release held output;
3. disconnect/clear live session;
4. delete product association/credentials transactionally;
5. persist verified state;
6. publish confirmed removal.

If it is disconnected, no live-session teardown is needed.

Destination:

- last saved Mouse removed -> `searching-first` + automatic first search;
- saved mice remain -> return to a valid Saved Devices page.

A later HOME entry with saved mice but no live connection starts saved search automatically.

### 3.7 Learn

`learn-the-keys` is never the boot root.

Its HOME action delegates to the single HOME resolver rather than hard-coding a destination.

Exact title/coordinates/control semantics are frozen by mbr-00 from the canonical product screen reference.

## 4. Async events

Screens consume semantic events such as:

```text
MouseReady(mouse_id, session_id)
MouseDisconnected(mouse_id, session_id, reason)
SavedSearchExpired(transaction_id)
PairNewExpired(transaction_id)
ProfileApplyConfirmed(mouse_id, profile)
ProfileApplyFailed(mouse_id, reason)
RemoveConfirmed(mouse_id)
RemoveFailed(mouse_id, reason)
PersistenceRecovered(previous_generation)
```

Transaction/session identity prevents stale completion from canceled/replaced operations from mutating current state.

## 5. Single-live-Mouse UI invariant

The UI never needs a `focused_mouse_id` separate from live connection truth.

When a Mouse is connected:

- it is the HOME name;
- it is the HOME profile summary target;
- it is the remapper target;
- its Saved Devices page is the only connected/cyan page.

When no Mouse is connected:

- remapper is not given an arbitrary saved target through HOME;
- HOME uses saved-search/retry states instead.

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
- didactic screens use their explicitly frozen token columns.

There is no count line such as `N DEVICES CONNECTED` to format or capacity-test.

## 7. Dynamic text policy to freeze

Before renderer acceptance freeze:

- Mouse-name truncation/ellipsis/scroll behavior;
- exact profile display strings (`DEFAULT` vs `STANDARD`);
- exact disconnected status text;
- capitalization/typo normalization;
- exact canonical didactic title and token columns.

No renderer guesses these from available pixels.

## 8. Golden layout/state tests

Host tests must assert:

- exact frozen literal rows;
- dynamic field substitution without metadata annotations;
- exact token start columns;
- selection/current/pressed color precedence;
- hint count/anchoring;
- hidden controls separated from visible text;
- no Keyboard/Composite product pages;
- no multi-connected HOME/count/focus state;
- `home-connected` requires exactly one live Mouse;
- `home-searching` requires saved mice + no live Mouse and emits/owns saved-search start semantics through application state;
- disconnect from `home-connected` immediately leads to `home-searching` and active saved search;
- saved-search timeout leads to `home-retry`;
- Pair New replacement never projects two connected mice.

## 9. Required mbr-00 UX output

mbr-00 must produce one canonical screen/control/transition table containing:

```text
screen_id
literal/dynamic rows
field formatting rules
selectable rows/order
visible controls
hidden controls
press feedback
release actions
async transitions
Back target
lock policy
live-Mouse requirement
search side effects
source decision
```

That table, not informal resemblance to BLU2USB, becomes the executable UX specification.
