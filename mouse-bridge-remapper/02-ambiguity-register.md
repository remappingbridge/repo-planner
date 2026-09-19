# Ambiguity and decision register

Status: **PLANNED**. This register contains only decisions that still matter after the 2026-09-20 single-connected-Mouse simplification.

Severity:

- **BLOCKER** — dependent implementation cannot be accepted until explicitly resolved.
- **MATERIAL** — architecture can proceed behind an abstraction, but affected behavior cannot be accepted.
- **EDITORIAL** — wording/layout must still be normalized before final UI acceptance.

## Closed decisions — do not reopen implicitly

The following older ambiguities are resolved by current product authority:

### Escape output

**RESOLVED.** Escape remains supported.

The product may expose a minimal fixed USB Keyboard output capability solely to emit synthetic `Escape` from Mouse remapping. This does not authorize Bluetooth Keyboard pairing/input or Composite product support.

### Multiple simultaneous Mouse connections

**RESOLVED by removal.** Only one Mouse may be connected/ready at a time. Multiple saved Mouse records remain supported.

Removed concepts:

- `N DEVICES CONNECTED` UI;
- connected-Mouse count limit such as 999;
- multi-Mouse runtime session set;
- simultaneous HIDS qualification;
- cross-Mouse button aggregation;
- UI focus/target selection among live mice;
- reconnect-many scheduling.

### HOME profile target

**RESOLVED.** Remapper actions target the single currently connected Mouse.

### Saved reconnect policy

**RESOLVED.** Whenever HOME is entered with saved mice and no live Mouse, start a bounded saved-device search automatically. The first saved Mouse that reaches ready state wins and the search stops. Timeout leads to `home-retry` / `DEVICE NOT FOUND`.

A disconnect/power-off of the live Mouse uses this same HOME resolver automatically.

### Pair New while connected

**RESOLVED.** Pair New is a replacement transaction. The current live Mouse is safely released/disconnected first but remains saved. Then the product searches for one unsaved Mouse; first valid winner becomes the sole live Mouse.

Pair New failure/cancel does not delete the previous saved Mouse and does not silently reconnect it inside Pair New.

### Saved Devices connected color

**RESOLVED.** Only the page for the single connected Mouse may show its name in cyan and `STATUS: CONNECTED`.

## AMB-001 — Final USB VID/PID/manufacturer/product strings

**Severity:** BLOCKER for final USB identity acceptance.

Structural USB behavior is settled:

- fixed identity from boot;
- Mouse HID output;
- minimal Keyboard HID output only for synthetic Escape;
- no CDC/debug interface;
- no Bluetooth-driven re-enumeration.

What remains open is the exact project-specific VID/PID, manufacturer string and product string.

Historical BLU2USB strings are evidence only and must not be copied automatically.

## AMB-002 — `DEFAULT REMAP` vs `STANDARD REMAP`

**Severity:** MATERIAL/EDITORIAL.

`MOUSE OPTIONS` says `DEFAULT REMAP`, while dedicated pages and HOME summary use `STANDARD` terminology.

They denote the same mapping. Internal profile identity must remain display-string independent.

## AMB-003 — Learn/search-first title wording

**Severity:** MATERIAL/EDITORIAL.

The supplied screen block says `PRESS TO LEARN KEYS`; earlier prose variants include `PRESS A KEY TO LEARN` and historical G06 used `PRESS TO LEARN A KEY`.

The canonical current screen reference uses `PRESS TO LEARN KEYS`, but mbr-00 must freeze the literal table and treat that freeze as acceptance authority.

## AMB-004 — Didactic character coordinates

**Severity:** MATERIAL/EDITORIAL.

The new coordinate declarations intentionally supersede historical G06 horizontal positions, but some source wording used “linha” where the context clearly meant a column.

mbr-00 must freeze exact 1-based token-column assertions from the current canonical screen reference. Renderer implementation may not infer positions from Markdown spacing.

## AMB-005 — `searching-first` naming aliases

**Severity:** EDITORIAL.

Older source prose sometimes says `searching-first-mouse`; current canonical screen ID is `searching-first`.

Implementation must use one canonical ID and may preserve the old phrase only as historical prose.

## AMB-006 — Pair New handling of already-saved candidates

**Severity:** MATERIAL.

Pair New is defined as new-only. A candidate already present in Saved Devices must not be accepted as the new pairing winner.

Still to freeze: whether such a candidate is silently ignored while the Pair New window continues, explicitly rejected with UI feedback, or causes another documented behavior.

It must not delete/overwrite the saved record.

## AMB-007 — `KEY B: BACK TRY SAVED` exact transition

**Severity:** MATERIAL.

Product-level behavior is constrained: when navigation reaches HOME with saved mice and no live connection, `home-searching` automatically starts saved search.

Still open is the exact one-step screen/navigation action from `retry-pair-new` on Key B. The implementation must not create a second independent reconnect mechanism.

## AMB-008 — `JOY LEFT: GO TO HOME` on `escape-active`

**Severity:** MATERIAL.

The current screen explicitly includes this action, while historical G06 generally removed special “Go To Home” behavior in favor of logical Back.

mbr-00 must decide whether the explicit new screen rule intentionally supersedes the old navigation rule or should be normalized.

## AMB-009 — Lock availability on screens where omitted

**Severity:** MATERIAL.

Some new screens print `KEY Y: LOCK`; others omit it. Historical G06 sometimes had hidden lock controls.

Every final screen needs an explicit control map. Omitted controls must not be inherited merely by analogy.

## AMB-010 — `FIRST MOUSE CONNECTED` lifetime/control semantics

**Severity:** MATERIAL.

The page must appear after the first Mouse is successfully saved/ready, but the complete release-action map is not yet frozen.

Visible text associates `OPEN HOME -> KEY Y` with the right-side controls and also presents lock/unlock teaching. mbr-00 must freeze exact interaction semantics.

## AMB-011 — Disconnected status word in Saved Devices

**Severity:** MATERIAL/EDITORIAL.

`STATUS: CONNECTED` is defined for the one live Mouse. The exact visible word for every saved-but-disconnected Mouse is not frozen (`SAVED`, `DISCONNECTED`, etc.).

Underlying state is unambiguous.

## AMB-012 — Long Mouse names

**Severity:** MATERIAL/EDITORIAL.

Dynamic Mouse names may exceed the 21-character semantic width. Storage should retain the best complete normalized name available, but projection needs a deterministic truncation/ellipsis/scroll policy.

Renderer must not invent this policy.

## AMB-013 — Search timeout constants

**Severity:** MATERIAL.

First-Mouse search is logically indefinite. Saved-device search and Pair New are bounded, but exact durations are not frozen.

Timing belongs to coordinator policy constants, not BLE parser logic.

## AMB-014 — Literal typo/capitalization normalization

**Severity:** EDITORIAL.

Historical source anomalies include `REMAPPED T0 ESCAPE`, `FORWARED`, `kEY Y`, stray backticks and similar transcription issues.

The current product manual already normalizes several obvious typos. mbr-00 must freeze the final literal screen table so renderer tests and physical acceptance use one source of truth.

## AMB-015 — Bluetooth Mouse transport scope

**Severity:** MATERIAL, low risk.

Current production scope is BLE HOGP Mouse because that is the accepted G06 Mouse baseline. Bluetooth Classic Mouse support is not planned.

mbr-00 should explicitly ratify BLE HOGP-only Mouse transport so implementation cannot expand scope opportunistically.

## Decision discipline

- An ambiguity is resolved by updating planning/product documentation, not by silently choosing in code.
- Typographical cleanup that changes displayed text is still part of the UI acceptance contract.
- Implementation may keep abstractions open for unresolved policy, but may not claim blocked behavior accepted.
- Resolved simultaneous-Mouse questions must not be reintroduced as “future-proofing”; unnecessary complexity is out of scope.
