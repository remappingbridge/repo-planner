# MBR-05 candidate evidence

Status: **IMPLEMENTED / PHYSICAL ACCEPTANCE PENDING**

Date: 2026-09-20

## Source

- product repo: `tiagooliveirajs/mouse-bridge-remapper`
- branch: `mbr/mbr-05-ble-mouse`
- PR: #7 — MBR-05: BLE Mouse passthrough and HOME visual amendment
- base: `mbr/rebuild-00-through-04`
- accepted predecessor: `8bebe26ff7554ffa811a58ea024cae5fe80a4a84`
- exact candidate head: `7724de294787b33d452616b2cd9124a5199e4419`

## Automated verification

GitHub Actions run `35509056712` completed successfully on the exact candidate head.

Host job:
- configure/build: PASS
- CTest: **10/10 PASS**
- core, ux, renderer, usb, hogp, output, bridge, qualification, architecture, canonical_screens: PASS

Pico 2 W job:
- pinned Pico SDK 2.2.0 setup: PASS
- production firmware build: PASS
- qualification firmware build: PASS
- UF2 verification: PASS
- artifact upload: PASS

## Artifact

Actions artifact:
- name: `mbr-05-pico2w-production-and-qualification`
- artifact ID: `10604858272`
- archive size: 1,918,227 bytes
- archive SHA-256: `9ce6d1b0809f0be1c1b78cc57092ac53480291762d86fda15327f2bbcdef0b21`

Files:
- `mouse_bridge_remapper.uf2` — 861,696 bytes — SHA-256 `be40b08c473c06f558b9769a661a8f197f10d3cea403e5a8b82ff21dd297b3d8`
- `mouse_bridge_remapper_qualification.uf2` — 95,744 bytes — SHA-256 `fd8bb312bd13549ef28c60803e0ee9b483e21329cb9d81b1450510b760960df6`
- `mouse_bridge_remapper.elf` — SHA-256 `8ecc7c8228632e8a8bcffa8a64897e987538fcd2a7a2ddf46fcce51a3bb4f46a`
- `mouse_bridge_remapper_qualification.elf` — SHA-256 `e3ea5735f1ac86f6330657b39ff2313ce823ef181480c4795171dc9af2d341e1`

## Physical gate

Physical acceptance is still open and belongs to the operator. Execute the numbered scenarios from
`docs/implementation/07-testes-manuais-mbr05.md` against the production UF2 above.

MBR-06 remains blocked until those required scenarios are accepted.
