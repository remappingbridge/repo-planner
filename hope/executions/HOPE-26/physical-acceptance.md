# HOPE-26 — physical acceptance

Status: **PENDING OPERATOR TEST**.

Candidate:

- branch: `hope/hope-26-help-pair-new`
- commit: `45dab4093d1b99ddd7cabba98322bfd8b3d14ee8`
- draft PR: `#9`
- UF2: `HOPE-26-help-pair-new-pico2w.uf2`
- size: **890,880 bytes**
- SHA-256: `d5127a85fede683c85f5666aa130fb848e03e2e094bdc0037a56c286134ce996`

## Required physical scenarios

1. Keep a saved/current Mouse connected and working.
2. Enter accepted `PAIR NEW MOUSE`; confirm normal Pair New starts.
3. While Pair New is active, press/release KEY X.
4. Exact `PAIR NEW DEVICE HELP` must appear.
5. Confirm exact text, black body and dark-magenta `ANY KEY: BACK` hint field.
6. Confirm entering Help cancels the active Pair New operation.
7. While Help is visible, keep an unsaved Mouse advertising/pairable; it must not be accepted.
8. Press/release KEY Y while Help is visible; it must behave only as Any Key Back and must not lock or turn off the display.
9. Re-enter Help and exit using another HAT control; the complete interaction must be consumed.
10. After Help exit, current incremental HOPE-26 must show the Pair New visual as the temporary retry placeholder.
11. Critically, no new 15-second Pair New search may start automatically after that Help return.
12. Keep an unsaved Mouse advertising after Help return; it must not pair automatically.
13. Current Mouse must remain connected/usable through Help entry and exit.
14. Start Pair New again through a normal valid route and confirm accepted HOPE-06 pairing still works.
15. Confirm no Keyboard/Composite behavior was introduced.

## Operator result

Operator declaration: **PENDING**.

Do not merge PR #9 and do not mark HOPE-26 ACCEPTED until the operator explicitly reports PASS.
