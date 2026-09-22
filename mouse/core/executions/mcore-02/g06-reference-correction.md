# MCORE-02 — G06 physical-reference correction

Status: **CORRECTIVE CANDIDATE READY FOR P01**.

Date: 2026-09-22.

## Reference baseline

The physically accepted blu2usb G06 implementation was used as the known-good BLE reference because the same tested mice connect reliably on that firmware.

Reference:

~~~text
repository: remappingbridge/blu2usb
branch: gate/g06-profiles-remap-logitech-hidpp
reference head inspected: 7eee024ad4ee726c5a85ffa2f32b9f47187878af
~~~

Known-good files inspected:

~~~text
src/bt_runtime/bt_runtime_pico.c
src/ble_hogp/ble_hogp_pico.c
include/btstack_config.h
src/storage/storage_pico.c
src/bt_runtime/g05_hog_host.gatt
docs/technical/04-g06-profiles-remap-hidpp-validation.md
docs/technical/05-g05-physical-acceptance-record.md
~~~

## Findings and corrections

### BTstack execution context

G06 performs scan/connect/disconnect transitions from BTstack callbacks/timers.

The failed MCORE-02 candidate crossed DEVICE_FOUND into portable Core and later called GAP APIs from the application loop. The corrected target now marshals Core requests through an atomic command mailbox and executes them in BTstack context using btstack_run_loop_execute_on_main_thread().

### Physical address type

G06 passes the advertising report address type unchanged into gap_connect(). MCORE-02 had normalized the physical connect address type. The corrected backend keeps normalization only for semantic identity and uses the raw advertised type for gap_connect().

### HCI sizing

Controller/host ACL packet counts were returned to the G06 accepted values:

~~~text
MAX_NR_CONTROLLER_ACL_BUFFERS = 3
HCI_HOST_ACL_PACKET_NUM = 3
~~~

MCORE-02 retains two connection/client slots required for Pair New:

~~~text
MAX_NR_HCI_CONNECTIONS = 2
MAX_NR_GATT_CLIENTS = 2
MAX_NR_HIDS_CLIENTS = 2
~~~

### G06 policies restored

Speculative changes from the failed attempts were removed. The current target again uses:

~~~text
passive scan: gap_set_scan_parameters(0u, 48u, 48u)
advertised HID Service UUID qualification
explicit non-mouse HID Appearance rejection
SM_AUTHREQ_SECURE_CONNECTION | SM_AUTHREQ_BONDING
hids_client_connect(... HID_PROTOCOL_MODE_REPORT ...)
~~~

## Candidate

~~~text
mouse-core head: 2c768e7b6c202c93b60c9c944f5aaa985a648766
workflow: 35697933226
host-debug: SUCCESS
host-asan-ubsan: SUCCESS
pico2-w-ble-qualification: SUCCESS
artifact id: 10681476972
UF2 SHA-256: 6acaa2d4e48837ffee55b5f1eef7fd74604c8a769e3dae1d67ea3b892f4f39c1
~~~

## Diagnostic behavior retained

- KEY Y >= 3 s: five fast flashes, clear product registry/bonds, reboot.
- FIRST scan: approximately 250 ms LED blink.
- Candidate selected / Core qualification pending: approximately 50 ms blink.
- HOGP authoritative session: solid LED.

## Physical state

Physical attempts 01 and 02 remain FAIL. This G06-aligned firmware has not yet been physically accepted. Only P01 is eligible; P02-P09 remain blocked until P01 passes.
