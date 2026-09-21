# MUI-03 — Deterministic Mock World

Status: **NOT STARTED**.

## Objective

Create the frontend-only product-view source and deterministic async result model so the UX can be developed without implementing a second backend.

## Dependencies

MUI-00 ACCEPTED; may proceed in parallel with MUI-01/MUI-02.

## Tasks

1. Define private frontend Product View state separate from UI navigation state.
2. Define private semantic intents and observable result/events; do not publish them as the stable UI↔Core contract.
3. Model request/pending/success/failure for user-visible product operations.
4. Implement deterministic virtual clock and scheduled events without real sleeping.
5. Implement in-memory simulated persistence sufficient for REBOOT/FACTORY RESET UX.
6. Support saved Mouse records, current connection truth, profile state, Custom draft/applied state, search purpose/outcome, and removal/handoff results needed by baseline screens.
7. Add scenario primitives and stale/late event generation hooks.
8. Explicitly avoid HCI/GATT/HIDS/TinyUSB/flash-sector simulation.

## Deliverables

- mock/product-view library
- virtual clock/scheduler
- in-memory mock persistence
- unit tests for deterministic transitions

## Automated acceptance

- [ ] identical input/event sequence yields identical state
- [ ] clock advancement is deterministic and no test sleeps
- [ ] success/failure/timeout/stale result paths are independently injectable
- [ ] reboot restores simulated persisted view state; factory reset clears it
- [ ] mock source has no BTstack/TinyUSB/flash implementation dependencies

## Human acceptance

- [ ] inspect example state traces for clarity and usefulness to UX work

## Forbidden scope

- real backend algorithms
- transport packet simulation
- public contract release
- screen rendering/navigation

## Rollback / rebuild point

If the mock starts reproducing backend internals, delete/rebuild it around semantic observable results while preserving scenario tests.

