# HOPE-06 — physical acceptance

Status: **PENDING OPERATOR TEST**.

Candidate:

- branch: `hope/hope-06-pair-new`
- commit: `6f4d8890a6011e679efb56bc098f81844632074a`
- draft PR: `#8`
- UF2: `HOPE-06-pair-new-pico2w.uf2`
- size: **890,368 bytes**
- SHA-256: `e118c3e2bd17f60b9315d7159ae11fb4c052103ba723a55f60b0dfcd3d3b9f02`

## Required physical scenarios

1. Reach Pair New from an available current route and confirm the exact canonical `PAIR NEW MOUSE` screen.
2. Confirm the old `PAIR MOUSE / SEARCHING BLE HID / TARGET MOUSE / AUTO SEARCH ACTIVE` visual never appears.
3. With one Mouse already connected, enter Pair New and continue moving/clicking it throughout candidate discovery; existing Mouse forwarding must remain uninterrupted and authoritative.
4. While Pair New is running, expose a second Mouse that is already saved/bonded. It must **not** be accepted as the new candidate and the same 15-second Pair New window must continue.
5. If available, expose a BLE HID device that is explicitly non-Mouse by HID appearance; it must not be accepted.
6. Start a fresh Pair New window and expose a genuinely new/unsaved BLE HOGP Mouse. Complete its pairing.
7. Until that candidate has completed security + HIDS + descriptor Mouse qualification, the old current Mouse must remain the only authoritative Mouse.
8. During candidate qualification, candidate movement/buttons must not leak to USB.
9. At successful handoff, any old held Mouse button or Escape/remap output must be released; no stuck state may remain.
10. After handoff, only the newly promoted Mouse must control USB; old Mouse input must no longer be authoritative.
11. Reboot and verify the newly paired Mouse remains saved/persisted.
12. Start Pair New again with a current Mouse, then press B before success. Pair New must cancel, current Mouse must remain unchanged/usable, and HOME resolution must occur.
13. Start Pair New again and let the full **15 seconds** expire without an unsaved candidate. Current Mouse must remain unchanged; search must stop. HOPE-07 retry text must not appear early.
14. After timeout, a newly advertising Mouse must not be accepted unless Pair New is explicitly entered again.
15. Start Pair New and press/release Y. Presentation must lock, Pair New must cancel, and current Mouse/saved state must remain intact.
16. Unlock with one complete HAT interaction. It must be consumed and normal HOME resolution must occur.
17. Repeat Pair New with no live Mouse. Cancel and timeout must not fabricate a connected Mouse.
18. Confirm accepted HOPE-01/02/08/24/09/25 screens and flows remain functional.
19. Confirm current profile/remap behavior and persistence remain functional before and after a successful handoff.
20. Confirm no Bluetooth Keyboard/Composite Pair New behavior was introduced.

## Operator result

Operator declaration: **PENDING**.

Do not merge PR #8 and do not mark HOPE-06 ACCEPTED until the operator explicitly reports PASS for this exact candidate.
