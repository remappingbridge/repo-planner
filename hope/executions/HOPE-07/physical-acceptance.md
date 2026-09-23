# HOPE-07 — physical acceptance

Status: **PENDING OPERATOR TEST**.

Candidate:

- branch: `hope/hope-07-retry-pair-new`
- commit: `790ee2754c89cf151e1d85f20cad4c64c674834e`
- draft PR: `#10`
- UF2: `HOPE-07-retry-pair-new-pico2w.uf2`
- size: **891,392 bytes**
- SHA-256: `096c387861a949085c48428f348315d210c18e8f73f5235e1dae6fb66565c067`

## Required physical scenarios

1. Keep a current saved Mouse connected and usable.
2. Enter accepted `PAIR NEW MOUSE` active search.
3. Let the full 15-second window expire without a new unsaved Mouse.
4. Exact retry screen must appear:
   `PAIR NEW MOUSE / NO NEW MOUSE OUTSIDE / THE LIST OF SAVED / DEVICES WAS FOUND`.
5. Confirm the active searching copy `TRYING TO CONNECT / A NEW MOUSE THAT ...` is no longer shown after timeout.
6. Verify black body and dark-magenta hint region beginning at `KEY A: RETRY NEW PAIR`.
7. Current Mouse must remain connected and usable.
8. Press A. Active `PAIR NEW MOUSE / TRYING TO CONNECT ...` must appear and a fresh 15-second Pair New operation must start.
9. Let that new attempt expire; retry screen must appear again.
10. Start Pair New, open `PAIR NEW DEVICE HELP` with X, then exit Help with any key. It must land directly on canonical retry screen.
11. On retry with current Mouse live, press B. Current HOME resolver placeholder must be reached and Mouse remains usable.
12. Repeat with current Mouse unplugged but still saved; B must enter `SEARCHING SAVED MOUSE` and start accepted saved-only search.
13. Press/release X on retry. HOPE-27 Help must not appear yet; retry screen remains.
14. Press/release Y on retry. Presentation must lock/off.
15. Unlock with one complete HAT interaction. It must be consumed and HOME must resolve normally.
16. Confirm a successful Pair New still performs the accepted HOPE-06 handoff.
17. Confirm no Keyboard/Composite Pair New behavior was introduced.

## Operator result

Operator declaration: **PENDING**.

Do not merge PR #10 and do not mark HOPE-07 ACCEPTED until the operator explicitly reports PASS.
