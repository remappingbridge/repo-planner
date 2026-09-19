# MBR-00 completion report

Gate: `mbr-00` — Provenance, decisions and contract freeze  
Status: **COMPLETE / ACCEPTED**

## Objective achieved

Converted the planning/product definition into an implementation-ready frozen contract, revalidated the accepted BLU2USB G06 provenance, resolved all decisions required by mbr-01 through mbr-07, incorporated the latest Pair New Help screens, and aligned destination product documentation and successor gates.

## Entry subjects

- Planner base at gate entry: `tiagooliveirajs/repo-planner@bc5d6913dfdb26eda89689fb2b1cfbb878070022`
- Destination product base at gate entry: `tiagooliveirajs/mouse-bridge-remapper@db2f96606240ffc7a2a4f3bac24773d34aec0e59`
- Accepted historical baseline: `tiagooliveirajs/blu2usb@7eee024ad4ee726c5a85ffa2f32b9f47187878af`
- Historical accepted G06 UF2 SHA-256: `d32819b99ec349cc36882aaeaf9e84000a98dc3dcd86238ed364fd01301c8f88`

Working branches:

- `mouse-bridge-remapper:mbr/mbr-00-contract-freeze`
- `repo-planner:mbr/mbr-00-contract-freeze`

Product documentation candidate reviewed before planner completion record: `fccba54344742c2210000cac0d3e40823d85e975`.

Planner contract package immediately before this completion record: `31d88a316da91325b900d75b93deee65015c7714`.

The accepted integration subject is the fast-forwarded `main` state after final consistency review; successor gates must re-read current `main` rather than assume the pre-record SHA above is the final planner head.

## Provenance observed

Accepted G06 tree and documentation were re-inspected at the exact SHA. Confirmed relevant source/build areas include:

- `src/app`
- `src/ble_hogp`
- `src/bt_runtime`
- `src/domain`
- `src/hat`
- `src/hid_aggregator`
- `src/interaction`
- `src/logitech_hidpp`
- `src/profiles`
- `src/remap`
- `src/renderer`
- `src/storage`
- related headers/build composition
- G04/G05/G06 technical validation docs
- product/architecture/UX contracts
- CI/toolchain files.

Detailed classification is `08-mbr-00-migration-manifest.md`.

## User-visible decisions frozen

1. Multiple mice may be saved; <=1 Mouse is authoritative/connected.
2. BLE HOGP is the only Mouse transport.
3. Pair New is 15-second new-only replacement search.
4. A healthy current Mouse remains connected/usable while Pair New searches.
5. Saved candidates are ignored for Pair New acceptance.
6. First qualified unsaved candidate triggers release-safe old->new handoff.
7. Timeout/cancel before handoff leaves current Mouse connected.
8. Exact `help-pair-new` and `help-retry-pair-new` text supplied by the user is canonical.
9. To reconnect a saved Mouse from Pair New flow, user unplugs current Mouse and Backs until HOME reaches SEARCHING.
10. HOME with saved mice + no live Mouse starts 8-second SEARCH_SAVED automatically.
11. FIRST_MOUSE uses repeating 8-second cycles until first success.
12. Saved-search timeout/cancel -> `DEVICE NOT FOUND`.
13. `STANDARD` is the canonical profile vocabulary; historical `DEFAULT` is alias only.
14. Saved Devices disconnected status is `DISCONNECTED`.
15. Long names display first 21 supported characters; fallback `UNKNOWN MOUSE`.
16. Escape remains synthetic USB Keyboard output only.
17. USB identity/interface shape is frozen by MBR-00.
18. No hidden Lock controls; didactic B/X/Y controls are explicit.
19. `JOY LEFT: GO TO HOME` on Escape Active is intentional.
20. New didactic token columns are frozen.

## USB frozen identity

- VID `0xCAFE`
- PID `0x4011`
- bcdDevice `0x0100`
- manufacturer `tiagooliveirajs`
- product `Mouse Bridge Remapper`
- no serial string
- interface 0 HID Mouse
- interface 1 minimal HID Keyboard for synthetic Escape
- no CDC/debug interface
- fixed from boot/no Bluetooth-driven re-enumeration

The VID is documented as a project/local development convention, not a commercial USB-IF allocation claim.

## Architecture conclusion

Frozen conceptual modules:

- domain
- mouse_registry
- mouse_session
- output_state
- profiles
- remap
- pairing_coordinator
- bt_runtime
- ble_hogp
- logitech_hidpp
- product_storage
- usb_hid
- interaction
- ui_projector
- renderer
- hat
- app

Forbidden speculative architecture includes multi-authoritative-Mouse managers, cross-Mouse aggregation, live-Mouse focus/capacity UI, Bluetooth Keyboard/Composite product modules, and debug CDC product identity.

## Documentation changed/created

### Destination product documentation

Updated on the MBR-00 branch:

- `README.md`
- `docs/manual/01-first-start-and-pairing.md`
- `docs/manual/02-home-and-connection.md`
- `docs/manual/03-remapping.md`
- `docs/manual/04-saved-devices.md`
- `docs/manual/05-controls-lock-and-help.md`
- `docs/manual/06-screen-reference.md`
- `docs/architecture/01-product-contract.md`
- `docs/architecture/02-system-architecture.md`
- `docs/architecture/04-bluetooth-lifecycle.md`
- `docs/architecture/05-remap-escape-usb.md`
- `docs/architecture/07-ui-renderer.md`
- `docs/architecture/08-verification-invariants.md`
- `docs/architecture/09-open-decisions.md`

Existing architecture authority/session/persistence documents remain part of the same product documentation set and must be re-read by successor gates.

### Planner

Updated/created:

- `00-authority-scope-and-precedence.md`
- `01-g06-migration-ledger.md`
- `02-ambiguity-register.md`
- `03-target-architecture.md`
- `04-ux-state-model.md`
- `05-gates.md`
- `06-execution-rules.md`
- `07-mbr-00-frozen-contract.md`
- `08-mbr-00-migration-manifest.md`
- `README.md`
- `requirements/2026-09-20-single-connected-mouse.md`
- `requirements/2026-09-20-pair-new-help-and-handoff.md`
- this completion report.

The original `requirements/2026-09-19-user-rules.md` remains preserved as provenance.

## Verification/evidence

Evidence class for this gate is documentary/repository observation.

Performed:

- revalidated exact accepted G06 commit exists;
- re-inspected accepted G06 tree/docs/build areas;
- revalidated destination/planner entry SHAs;
- froze all mbr-00 product decisions in destination docs and planner;
- updated successor gate scopes/regressions;
- prepared branch-based changes for fast-forward integration;
- consistency review required before main integration.

Not applicable / not performed:

- firmware implementation;
- host/Pico build;
- UF2 generation;
- hardware flashing;
- physical acceptance.

These are intentionally outside mbr-00.

## Limitations / explicit non-claims

- No documented firmware feature is claimed implemented merely because the product contract is frozen.
- The G06 UF2 hash is historical baseline evidence, not an MBR artifact.
- `0xCAFE` is not claimed as a commercial USB-IF VID allocation.
- Physical timing/connection behavior will be validated only in the later physical gates.

## Gate conclusion

**MBR-00 acceptance criteria are satisfied.** No unresolved product decision required by mbr-01 through mbr-07 remains.

## Next executable point

`mbr-01 — Clean bootstrap and architecture enforcement`.

Entry requirement: re-read current `main` of both product/planner, this completion report, frozen contract, migration manifest and exact G06 baseline before changing firmware.
