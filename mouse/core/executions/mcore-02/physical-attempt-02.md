# MCORE-02 — Physical attempt 02

Status: **FAIL — SUPERSEDED BY DIAGNOSTIC/INTEROPERABILITY CANDIDATE**.

Date: 2026-09-22.

## Reported result

The corrected active-scan candidate was flashed.

Observed:

~~~text
KEY Y held for more than 5 seconds:
no visible acknowledgement was perceived.

Mouse placed in BLE pairing mode:
no connection occurred.
~~~

P01 therefore remains FAIL.

## Interpretation

The KEY Y GPIO mapping is confirmed against the previously accepted HAT implementation:

~~~text
KEY A = GPIO15
KEY B = GPIO17
KEY X = GPIO19
KEY Y = GPIO21
~~~

The previous qualification firmware rebooted into the same FIRST state without a dedicated
reset acknowledgement, so a successful reset could look identical to no reset.

The BLE failure also showed that active scan + UUID/Appearance fallback was still not
sufficiently interoperable for the tested devices.

## Corrective changes after attempt 02

The next candidate adds:

1. visible factory-reset acknowledgement:
   - five fast LED flashes before reboot;

2. physical stage diagnostics:
   - FIRST scanning: ~250 ms blink;
   - candidate detected / connection qualification: ~50 ms blink;
   - authoritative HOGP-ready Mouse: solid LED;

3. broader BLE discovery:
   - advertising HID UUID/Appearance is no longer required;
   - only an explicitly non-mouse HID Appearance is rejected;
   - HIDS client establishment is the definitive Mouse/HOGP proof;

4. legacy BLE HID compatibility:
   - bonding remains required;
   - LE Secure Connections is supported but no longer mandatory for every peer;
   - legacy BLE pairing is allowed for legitimate older HID devices;

5. host regression coverage:
   - hintless candidate is allowed to progress to HIDS proof;

6. architecture guard:
   - active scan required;
   - HIDS proof required;
   - mandatory-Secure-Connections policy rejected.

MCORE-02 remains **PHYSICAL ACCEPTANCE PENDING**.
