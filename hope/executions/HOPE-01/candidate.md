# HOPE-01 — candidate

Status: **CANDIDATE READY / PHYSICAL ACCEPTANCE PENDING**.

Date: 2026-09-23.

## Product candidate

- repository: `remappingbridge/remappingbridge`
- accepted base: `main@16b9a3a2f164f08e3a9c25fc39a4767b04f48f39`
- branch: `hope/hope-01-searching-first`
- candidate commit: `987afd16e951160860c77bd9f304dca586c339d0`
- draft PR: `#2`
- PR state: **must remain unmerged until operator physical acceptance**

## Provenance

- accepted HOPE-00/G06 base: `16b9a3a2f164f08e3a9c25fc39a4767b04f48f39`
- original BLU2USB G06: `7eee024ad4ee726c5a85ffa2f32b9f47187878af`
- Mouse UI Layout 1.0: `e8adad7919e931c92515bf655ef4050876a8e7a9`

## Scope implemented

Only the canonical `searching-first` screen was introduced:

~~~text
SEARCHING FIRST MOUSE
PRESS TO LEARN KEYS
WHILE WAIT CONNECTION
       JOY UP
  JOY    JOY    JOY
  LEFT  PRESS  RIGHT
      JOY DOWN
 KEY A         KEY X
 KEY B         KEY Y
~~~

Observable behavior:

- full 240x240 dark-magenta didactic background;
- title uses the existing title/magenta tone;
- rows `PRESS TO LEARN KEYS` and `WHILE WAIT CONNECTION` use the standard body/yellow tone;
- HAT labels rest in action/light-gray;
- the exact visible token(s) for a held HAT control become white;
- release restores the resting color;
- joystick, A, B, X and Y are all didactic only;
- B does not navigate;
- Y does not lock;
- no HAT control cancels first-Mouse discovery.

The implementation reuses the G06 renderer and interaction primitives. No mouse-ui architecture, contract layer or separate Core was imported.

## Startup integration

G06 does not yet have the future Saved Devices registry. HOPE-01 therefore uses the already-existing persistent BTstack LE device database as the minimal first-start discriminator:

- no persisted G06 Mouse bond -> `searching-first`;
- existing G06 Mouse bond -> preserve inherited G06 startup behavior until later HOME/search gates replace it.

This does not introduce Bluetooth Keyboard or Composite pairing.

## Post-success behavior in this gate

`first-mouse-connected` belongs to HOPE-02 and was deliberately not implemented early.

When the BLE HOGP pipeline reaches the existing G06 READY/`CONNECTED` event while `searching-first` is displayed, HOPE-01 temporarily transfers to the inherited G06 HOME. HOPE-02 will replace that temporary successor with the canonical `first-mouse-connected` screen.

## Changed files

Compared with accepted HOPE-00 main:

- `include/blu2usb/ble_hogp/ble_hogp.h`
- `include/blu2usb/ux_model/ux_model.h`
- `src/app/main.c`
- `src/ble_hogp/ble_hogp_pico.c`
- `src/renderer/renderer.c`
- `src/ux_model/ux_model.c`
- `tests/CMakeLists.txt`
- `tests/test_hope01_searching_first.c`

No HOPE-02+ screen was added.

## Automated verification

GitHub Actions run: `35815523922` — **SUCCESS**.

- `host-architecture`: **SUCCESS**
  - configure host build: PASS
  - build inherited + HOPE-01 tests: PASS
  - host/architecture/screen-contract tests: PASS
- `pico2-w-production`: **SUCCESS**
  - pinned ARM toolchain: PASS
  - pinned Pico SDK: PASS
  - Pico 2 W configure: PASS
  - production build: PASS
  - UF2 verification: PASS
  - artifact upload: PASS

The focused HOPE-01 test proves exact screen rows, full didactic background, yellow prompt, gray resting controls, white held feedback, frozen coordinates, B inert, Y inert and no navigation/lock from any HAT control.

## Candidate UF2

GitHub Actions artifact:

- artifact id: `10731516620`
- name: `blu2usb-picow-production-pico2w`
- ZIP size: 324,056 bytes
- ZIP digest reported by GitHub: `sha256:1aca428309f4c00e5b3eaeffc6b29d634eee681585bcdf2ebe0557ea9d1c690e`

Extracted firmware:

- file: `blu2usb_picow.uf2`
- size: **880,640 bytes**
- SHA-256: `2ef475f7d7fd26914cfd8e86ff2a990b03d923696155312c8281956d8826a03b`

## Gate state

HOPE-01 is **not ACCEPTED**.

It may be promoted only after the operator flashes this exact candidate and explicitly reports physical PASS.
