# HOPE-10 — physical acceptance

Status: **PENDING OPERATOR TEST / NOT ACCEPTED**.

Candidate:

- branch: `hope/hope-10-remapper-options`
- commit: `45407b30d8f438586ad4d380c65ce5dfb2415b64`
- draft PR: `#15`
- current main restoration commit: `3471e983cb7ec048c9ebb4b057633ff1b028e3aa`
- UF2: `HOPE-10-remapper-options-pico2w.uf2`
- size: **896,512 bytes**
- SHA-256: `42a40d9079bdad2cf22cec939d69862c0ba1912c1bc6e4d7f54a5d7ec265fdfa`

PR #14 was prematurely merged before physical acceptance and immediately functionally reverted. This event does not count as acceptance. The candidate to test is still commit `45407b30d8f438586ad4d380c65ce5dfb2415b64` and its UF2 above.

## Required physical scenarios

1. Reach accepted home-connected with a live Mouse.
2. Select the profile/remap summary and press Joy Press.
3. Confirm exact `MOUSE OPTIONS` screen with only:
   - PASSTHROUGH
   - STANDARD REMAP
   - ESCAPE REMAP
   - CUSTOM REMAP
4. Confirm `PAIR MOUSE` is absent from this screen.
5. Confirm `DEFAULT REMAP` is now displayed as `STANDARD REMAP`.
6. Verify four-option Up/Down wrap.
7. Verify current active profile row is cyan.
8. Move selection onto the active row; it must become white.
9. Move selection away; active row must return to cyan.
10. Open Passthrough while it is active; existing active feedback screen must open.
11. Open Passthrough while another profile is active; existing apply screen must open.
12. Repeat active/inactive routing for Standard.
13. Repeat active/inactive routing for Escape.
14. Open Custom; existing custom-edit flow must open.
15. Press B from remapper-options; HOME must resolve normally.
16. Confirm Joy Left also returns HOME, matching current Mouse UI v1 behavior.
17. Press X; HOPE-29 Help must not appear yet.
18. Press Y; global lock must still work.
19. Unlock; interaction must be consumed and HOME resolver used.
20. Confirm Pair New is still available from HOME and functions normally.
21. Confirm profile application, remap behavior, Escape synthetic output and persistence remain functional.
22. Confirm no Keyboard/Composite pairing behavior was introduced.

## Operator result

Operator declaration: **PENDING**.

Do not merge PR #15 until the operator explicitly accepts this exact candidate.
