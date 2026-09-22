# MCORE-02 — Physical attempt 01

Status: **FAIL — SUPERSEDED BY CORRECTIVE CANDIDATE**.

Date: 2026-09-22.

## Reported result

~~~text
P01 FAIL
No tested BLE mouse connected.
~~~

The remaining physical scenarios were not executed because FIRST discovery/pairing is a
prerequisite.

## Failing firmware

~~~text
mouse-core branch:
mcore/mcore-02-ble-session-lifecycle

head:
1a0970cdeff24bb279d5ef3bc0b787a9a1845ae7

workflow:
35694144945

UF2 SHA-256:
635e301f0c01d413fdfb2725ad6a445f460aec21eb79e4915a04d985d8e1eb18
~~~

Host and target builds were green, but physical P01 proved that the discovery policy was
too restrictive for the tested real mice.

## Root-cause analysis

The target used:

1. passive BLE scanning; and
2. a hard requirement that the HID Service UUID be present in the advertising report
   before a peer could even be attempted.

That assumption is not sufficiently interoperable for physical BLE HOGP devices. A mouse
may place HID/name/appearance information in scan response data or advertise an explicit
Mouse/Generic-HID appearance without carrying the HID Service UUID in the primary
advertising PDU.

The HOGP service is ultimately proven by `hids_client_connect()`; therefore advertising
metadata must be treated as qualification hints, not as definitive service proof.

## Corrective change

The superseding candidate:

- switches scanning to active scan;
- accepts HID Service UUID **or** explicit Mouse/Generic-HID Appearance as a discovery
  qualification hint;
- keeps explicit non-mouse HID appearances rejected;
- still requires successful HIDS client establishment before SESSION_READY;
- adds a host test for Mouse Appearance without advertised HID UUID;
- adds an architecture guard preventing regression to passive/UUID-only discovery.

MCORE-02 remains unaccepted until the corrected candidate passes P01-P09 physically.
