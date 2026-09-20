# Experimental MBR-06/07/08 integrated recovery — candidate evidence

Status: **IMPLEMENTED THROUGH MBR-08 / PHYSICAL ACCEPTANCE PENDING**

Date: 2026-09-20

## Isolation

This execution is intentionally isolated from the accepted baseline.

- product branch: `experimental/mbr08-integrated-recovery-20260920`
- planner branch: `experimental/mbr08-integrated-recovery-20260920`
- base: MBR-05 `7f294a7fac9eebe226ad66c6b572582c5e483421`
- integrated implementation commit: `f13611d9bc6d28c54250445cbb268271f58c8efa`
- final product branch HEAD: `4297054d0573ce908ec81b2e2b82d9d428a5eee8`
- no merge into `main`, `mbr/rebuild-00-through-04`, or `mbr/mbr-05-ble-mouse`

The user explicitly authorized bypassing intermediate physical-gate dependency so MBR-06, MBR-07 and MBR-08 could be implemented and corrected together. That bypass permits implementation sequencing only; it does not declare physical PASS.

## Implemented scope

### MBR-05 recovery items
- BLE discovery accepts HID evidence split between advertisement and scan response.
- Mouse appearance may qualify discovery without requiring the HID UUID in one packet.
- FIRST 8-second renewal no longer cancels in-flight connection/security/HIDS qualification.
- identity/public address aliases are normalized for saved/new filtering.
- ancillary non-keyboard HID collections no longer cause a valid Mouse to be rejected.
- USB mount/resume releases output without tearing down Bluetooth.
- radio initialization retry and late-cancellation safety are implemented.

### MBR-06
- Passthrough, Standard, Escape and Custom runtime mappings.
- per-Mouse confirmed profile, global applied Custom template, persistent dirty draft.
- source ownership/refcounts and release-safe profile changes.
- dual-slot versioned CRC32 product storage with integrity fallback and write verification.
- product state separated from BTstack credentials.
- bounded saved reconnect support.
- Logitech HID++ Forward diversion/hold/release adaptation with generic fallback.

### MBR-07
- repeated FIRST search cycles, 8-second SEARCH_SAVED, 15-second Pair New.
- one authoritative Mouse plus one non-authoritative replacement transport.
- saved peers ignored as Pair New winners.
- old Mouse remains usable while new Mouse is discovered/qualified.
- release/disconnect/persist/promote handoff.
- persistent registry up to 16 mice.
- transactional removal with pending tombstone and credential-cleanup recovery.
- last saved removal returns to first-Mouse search.
- stale/late session completion isolation.

### MBR-08
- real profile, Custom draft/apply and removal requests wired to runtime/storage.
- real asynchronous connection/search state projection.
- Saved Devices live/disconnected projection without stealing the current page.
- Help ownership/return and preserved search deadline.
- HOME resolver, Pair New flow, Lock/Learn behavior and existing 30-screen literal/layout contract retained.
- HOME title/suffix and gray/white action styling retained.

## Final automated verification

GitHub Actions run: `35535753460`
Final product HEAD: `4297054d0573ce908ec81b2e2b82d9d428a5eee8`

Host:
- **14/14 PASS**
- core
- ux
- renderer
- usb
- hogp
- output
- bridge
- storage
- profiles
- integrated
- radio_adapter (real adapter compiled against pinned BTstack headers/event utilities with simulated controller/GATT/security events)
- qualification
- architecture
- canonical_screens

Pico 2 W:
- Pico SDK 2.2.0, SHA `a1438dff1d38bd9c65dbd693f0e5db4b9ae91779`
- BTstack `501e6d2b86e6c92bfb9c390bcf55709938e25ac1`
- ARM GCC 13.2.1
- Release build: PASS
- production UF2 verification: PASS
- qualification UF2 verification: PASS

Final Actions artifact:
- name: `mbr-08-experimental-pico2w-production-and-qualification`
- artifact ID: `10612770836`
- archive size: 1,965,187 bytes
- archive SHA-256: `23cb18fd27ec2956c5fcf47a2326e1b5f2336874da34e61a94d10522ef101bf3`

Production:
- file: `mouse_bridge_remapper.uf2`
- size: **880,640 bytes**
- SHA-256: `c9ded48b8c4e61459829eaa8cb2db777d8a2bd246ea57055aeedcde25846427e`

Qualification:
- file: `mouse_bridge_remapper_qualification.uf2`
- size: 96,256 bytes
- SHA-256: `97836b2e2db8670ff8b6ef3759afa37e9a97fb86d1656436f52d24a331e8b2ba`

The final documentation-only manifest commit rebuilt to the same UF2 hashes as the integrated implementation commit.

## Physical acceptance

Physical acceptance remains pending. Execute the 37 numbered scenarios in product repository
`docs/implementation/08-manual-tests.md` using the production UF2 above.

MBR-09 and MBR-10 are outside this recovery and remain backlog.
