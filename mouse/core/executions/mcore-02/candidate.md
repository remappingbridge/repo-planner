# MCORE-02 candidate evidence

Status: **IMPLEMENTED CANDIDATE — PHYSICAL BLE ACCEPTANCE PENDING**.

Date: 2026-09-22.

## Gate

MCORE-02 — BLE Session Lifecycle

Dependency: **MCORE-01 ACCEPTED**.

Released contract baseline remains:

~~~text
remappingbridge/mouse
UI↔Core Contract v1.0.0
release commit:
b26f77e27372bfcd7aa54235354b163394484a67
stable ref:
release/ui-core-v1.0.0
~~~

## Implementation repository

~~~text
repository: remappingbridge/mouse-core
branch: mcore/mcore-02-ble-session-lifecycle
rollback base:
5a26f66b60e22464f54f79f1511b66d84c5cae68
candidate head:
1a0970cdeff24bb279d5ef3bc0b787a9a1845ae7
~~~

## Implemented lifecycle

Portable Core now owns:

- FIRST search qualification;
- SAVED-only reconnect qualification;
- PAIR_NEW unsaved-only qualification;
- exact 8000 ms FIRST activity;
- exact 8000 ms SAVED activity;
- exact 15000 ms PAIR_NEW activity;
- search cancellation;
- stale/late candidate rejection;
- zero/one authoritative current Mouse;
- Pair New candidate kept non-authoritative during qualification;
- ordered handoff publication;
- physical disconnect publication;
- search/activity and connection notifications.

The portable lifecycle contains no HCI/GATT/Pico types.

## Pair New authority invariant

During Pair New the target backend can temporarily maintain two physical BLE links:

~~~text
A = current authoritative Mouse
B = non-authoritative replacement candidate
~~~

Product authority remains:

~~~text
authoritative_ready_mouse_count <= 1
~~~

Candidate readiness does not immediately mutate `current_mouse_id`.

The flow is:

1. candidate becomes HOGP-ready;
2. PAIR_NEW search result becomes FOUND;
3. HANDOFF operation becomes PENDING;
4. old current link is asked to disconnect;
5. candidate is durably adopted only at handoff commit;
6. Snapshot moves previous current -> candidate in one commit;
7. CONNECTION_CHANGED precedes HANDOFF SUCCEEDED.

## Eligibility

FIRST:

- valid only with no saved Mouse and no current Mouse;
- accepts an unsaved BLE HID candidate.

SAVED:

- accepts only identities present in the persistent registry;
- unsaved advertisements are ignored.

PAIR_NEW:

- requires an existing current saved Mouse;
- already-saved candidates are ignored;
- only an unsaved candidate can enter replacement qualification.

If the current Mouse physically disconnects during PAIR_NEW before candidate readiness,
the search remains PAIR_NEW; it does not silently turn into SAVED search.

## Identity

BLE peer identities use a disjoint high-bit namespace:

~~~text
local Core registry IDs:
0x0000... < 0x8000_0000_0000_0000

BLE transport-stable IDs:
high bit = 1
remaining bits = normalized BLE identity type + identity address
~~~

Resolved identity address types are normalized to the same namespace used by the LE
device database.

This prevents aliasing with the MCORE-01 monotonic local allocator.

## Physical target backend

Target:

~~~text
Raspberry Pi Pico 2 W
RP2350 + CYW43439
PICO_BOARD=pico2_w
Pico SDK 2.2.0
gcc-arm-none-eabi package 15:13.2.rel1-2
~~~

Target backend owns:

- CYW43 initialization;
- BTstack HCI/GAP/SM;
- BLE Central scanning;
- secure pairing;
- bonding;
- resolving-list loading for saved privacy identities;
- HIDS client lifecycle;
- physical link connect/disconnect;
- two-link Pair New qualification capacity;
- BLE event queue into portable Core.

BTstack limits for this qualification target include:

~~~text
MAX_NR_HCI_CONNECTIONS 2
MAX_NR_HIDS_CLIENTS 2
MAX_NR_LE_DEVICE_DB_ENTRIES 16
ENABLE_LE_SECURE_CONNECTIONS
~~~

Bluetooth Keyboard/Classic/Composite input is not introduced.

Raw HOGP report normalization/remap remains MCORE-03+ scope.

## Physical persistence

The qualification target includes a real RP2350 flash persistence seam.

Product registry state and SDK Bluetooth credentials have independent ownership.

Product persistence uses two flash sectors, CRC and read-back verification, preserving
the MCORE-01 durable commit boundary on physical flash.

Factory-reset qualification clears both:

- product registry storage;
- BTstack LE device database bonds.

## Qualification firmware

Artifact:

~~~text
name:
mcore02-ble-qualification-pico2w

file:
mcore_ble_qualification.uf2

candidate convenience filename:
mcore02-ble-qualification-pico2w.uf2

UF2 SHA-256:
635e301f0c01d413fdfb2725ad6a445f460aec21eb79e4915a04d985d8e1eb18

GitHub artifact id:
10679003858
~~~

The image intentionally has:

- real Core lifecycle;
- real BLE backend;
- real physical persistence;
- HAT qualification controls;
- Pico wireless LED state feedback;
- inert USB-output seam.

It intentionally does not contain product USB HID/remap implementation.

## Qualification controls

~~~text
KEY A / GPIO15
  contextual FIRST/SAVED search when no Mouse is current

KEY B / GPIO17
  cancel current search

KEY X / GPIO19
  PAIR_NEW while a Mouse is current

KEY Y / GPIO21 held >= 3 s
  factory reset product registry + BLE bonds + reboot
~~~

LED:

~~~text
off       no current Mouse and no active search
solid     one authoritative current Mouse
250 ms    FIRST blink
500 ms    SAVED blink
100 ms    PAIR_NEW blink
~~~

## Host/state test coverage

`tests/test_mcore02.c` covers:

1. FIRST discovery, persistent adoption and publication;
2. SAVED-only reconnect;
3. unsaved candidate rejected by SAVED;
4. saved candidate rejected by PAIR_NEW;
5. candidate remains non-authoritative before handoff;
6. ordered old->new Pair New handoff;
7. single-authority invariant throughout handoff;
8. PAIR_NEW cancellation preserving old current;
9. late candidate readiness ignored after cancel;
10. PAIR_NEW timeout at exactly 15000 ms preserving old current;
11. FIRST timeout at exactly 8000 ms;
12. SAVED timeout at exactly 8000 ms;
13. old-current disconnect during Pair New does not change search class.

## Architecture guard

`cmake/Mcore02BleGuard.cmake` proves:

- portable Core owns semantic search/handoff behavior;
- target technology remains under `target/pico/`;
- no BTstack/CYW43 headers leak into portable Core;
- target has two physical BLE link/HIDS capacity for Pair New;
- no TinyUSB/remap implementation is introduced at this gate;
- no Bluetooth Keyboard/Classic transport scope is introduced.

Final result: **PASS**.

## Final CI

~~~text
repository:
remappingbridge/mouse-core

workflow:
mouse-core MCORE-02 BLE lifecycle

run:
35694144945

head:
1a0970cdeff24bb279d5ef3bc0b787a9a1845ae7

conclusion:
SUCCESS
~~~

Jobs:

- host-debug — SUCCESS
- host-asan-ubsan — SUCCESS
- pico2-w-ble-qualification — SUCCESS

Host verification:

~~~text
UI-Core v1.0.0 release validation PASS
mouse-core UI-Core contract declaration PASS: v1.0.0 provider
MCORE-00 architecture guard PASS
MCORE-01 registry architecture guard PASS
MCORE-02 BLE architecture guard PASS

1/8 uic07_core_conformance                 PASS
2/8 mcore00_foundation                     PASS
3/8 mcore01_registry_persistence           PASS
4/8 mcore02_ble_session_lifecycle          PASS
5/8 uic07_architecture_guard               PASS
6/8 mcore00_architecture_guard             PASS
7/8 mcore01_registry_architecture_guard    PASS
8/8 mcore02_ble_architecture_guard         PASS

100% tests passed, 0 failed out of 8

UIC-07 probe PASS:
rev=201 current=2 saved=2 notifications=3
~~~

Target verification:

~~~text
PICO_BOARD=pico2_w
configure PASS
ARM build PASS
UF2 generated PASS
artifact upload PASS
UF2 SHA-256:
635e301f0c01d413fdfb2725ad6a445f460aec21eb79e4915a04d985d8e1eb18
~~~

## Physical acceptance procedure

Canonical physical procedure:

~~~text
remappingbridge/mouse-core
docs/qualification/mcore-02-physical.md
~~~

Required scenarios:

- P01 FIRST discovery/pairing;
- P02 physical disconnect;
- P03 SAVED-only reconnect + reboot/bond persistence;
- P04 PAIR_NEW rejects already-saved Mouse;
- P05 PAIR_NEW unsaved candidate + ordered handoff;
- P06 PAIR_NEW cancellation;
- P07 PAIR_NEW timeout;
- P08 FIRST/SAVED physical timing;
- P09 factory reset.

Human timing tolerance is ±1 second for LED/stopwatch observation while host tests prove
exact 8000/15000 ms boundaries.

## Scope control

Implemented:

- CYW43/BTstack BLE lifecycle;
- security/bonding;
- physical discovery and reconnect;
- FIRST/SAVED/PAIR_NEW separation;
- single-authority arbitration;
- Pair New two-link physical qualification;
- contract current/search/result publication;
- cancellation/timeout behavior;
- RP2350 flash registry persistence;
- physical qualification firmware;
- target build CI;
- physical test procedure.

Not implemented:

- normalized canonical HID input;
- remap engine;
- USB HID product output;
- MCORE-03 work;
- Bluetooth Keyboard/Classic product scope.

## Acceptance checklist

Automated/documentary:

- [x] host/state tests plus target build green
- [x] saved candidate rejected by Pair New
- [x] no two authoritative sessions
- [x] cancel/timeout leaves valid current session
- [x] released UI↔Core v1.0.0 conformance remains green
- [x] target UF2 produced and hashed
- [x] physical procedure documented

Human:

- [ ] physical BLE scenarios P01-P09 accepted on target

MCORE-03 remains blocked until MCORE-02 receives explicit physical human acceptance.
