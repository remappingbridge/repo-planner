# 2026-09-20 — Single connected Mouse simplification

Status: **CURRENT PRODUCT DECISION**. This requirement supersedes earlier simultaneous-mouse rules wherever they conflict.

## 1. Live connection capacity

- The product may keep multiple mice saved.
- Only **one mouse may be connected at a time**.
- There is no multi-connected HOME state, no connected-device count and no simultaneous-Mouse capacity target.

## 2. Pair New replacement rule

When the user manually starts `PAIR NEW MOUSE` while a mouse is connected:

1. the current mouse stops being the live connection;
2. its held output is released safely;
3. it is disconnected cleanly;
4. its saved record and bond remain intact;
5. Pair New searches for an unsaved mouse;
6. the first valid unsaved candidate accepted becomes the only connected mouse;
7. that Pair New transaction stops after the first success.

If Pair New fails or is canceled, the former mouse remains saved but disconnected. Pair New must not silently reconnect it. Normal HOME behavior handles later saved-device reconnection.

## 3. HOME resolver

HOME behavior is unified:

```text
no saved mouse
  -> searching-first

saved mouse exists + one connected mouse
  -> home-connected

saved mouse exists + no connected mouse
  -> home-searching + start bounded saved-device search automatically
```

If the bounded saved-device search finds a saved mouse, the first one that reaches ready state becomes the sole connected mouse and search stops.

If none is found before timeout, HOME transitions to `home-retry` / `DEVICE NOT FOUND`.

## 4. Connected-mouse power-off/disconnect

If the connected mouse is powered off, leaves range or otherwise disconnects:

- release held Mouse/Escape output from that session;
- clear the live connection;
- because saved mice still exist, enter `home-searching`;
- automatically start the bounded saved-device search;
- if no saved mouse reconnects before timeout, finish at `DEVICE NOT FOUND`.

The disconnect path uses the same HOME/saved-search logic as startup and navigation; it is not a separate reconnect policy.

## 5. Saved Devices color/status

- Saved Devices continues to show one saved mouse per page.
- Only the single currently connected mouse may show `STATUS: CONNECTED`.
- Only that mouse's name on line 2 is cyan.
- At most one Saved Devices page can be cyan at a time.

## 6. HOME connected screen

`home-connected` always shows the connected mouse name on line 2.

The former rule to display `N DEVICES CONNECTED` for multiple live mice is removed.

The remap summary and remapper actions always refer to the single connected mouse, eliminating the need for a UI focus/target-selection rule.

## 7. Architectural consequence

Remove or prohibit unnecessary simultaneous-mouse machinery:

- no set of live HOGP sessions;
- no cross-mouse button ownership aggregation;
- no simultaneous-HIDS capacity qualification;
- no multi-connected UI focus model;
- no per-live-mouse concurrent HID++ contexts;
- no `N DEVICES CONNECTED` limit such as 999.

Retain only the complexity still required for correctness:

- persistent registry of multiple saved mice;
- one optional live session with generation identity for stale-callback rejection;
- held-state safety within the current session, including two physical source buttons mapping to the same output;
- safe teardown before replacement/removal;
- bounded saved search and Pair New transactions;
- synthetic USB Escape exception;
- G06 persistence/reconnect/HID++ regressions applicable to one live mouse.

## 8. Scope unchanged

- Bluetooth Keyboard pairing remains excluded.
- Bluetooth Composite pairing remains excluded.
- Escape remains supported only as synthetic USB Keyboard output produced by mouse remapping.
