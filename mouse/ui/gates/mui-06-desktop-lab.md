# MUI-06 — SDL2 Desktop Laboratory

Status: **NOT STARTED**.

## Objective

Deliver the C-only Debian desktop application around the pure frontend core, including the dark development shell, requested visual controls, and precise semantic element inspection.

## Dependencies

MUI-05 ACCEPTED.

## Tasks

1. Add SDL2 only under the desktop platform/shell layer.
2. Create dark high-contrast development shell distinct from product framebuffer.
3. Present the logical 240×240 RGB565 framebuffer with pixel-preserving nearest-neighbor scaling.
4. Implement exact scale presets 75%, 100%, 125%, 150%, 200%, and 300%.
5. Implement virtual backlight slider/range 0–1000% with quick presets 0/100/300/500/750/1000%.
6. Keep framebuffer unchanged by scale/backlight; Lock forces effective 0% and unlock restores selected gain.
7. Implement keyboard mappings and on-screen virtual HAT with distinct press/release feedback.
8. Expose virtual Mouse name/ID, connect/disconnect, +1/+8/+15 second controls, HOME, reboot, and factory reset against mocks.
9. Add screen/state panel and event log.
10. Implement element inspector: pointer hit-test, keyboard prev/next traversal, shell-side outline, metadata panel, and copyable compact bug reference.
11. Ensure inspector overlay never changes product framebuffer/hash.
12. Support optional framebuffer capture (PPM or equivalent simple deterministic artifact) for bug evidence.
13. Add desktop calculations/tests for scale, gain, hit testing, and inspector reference formatting.

## Deliverables

- `mouse-ui` Debian desktop executable in C
- dark SDL2 development shell
- scale/backlight controls
- virtual HAT/keyboard input
- state/event panels
- semantic element inspector and copyable bug reference
- desktop adapter tests/build documentation

## Automated acceptance

- [ ] desktop builds without SDL leaking into core libraries
- [ ] all six scale presets produce expected presented dimensions
- [ ] backlight covers 0–1000%, saturates presentation channels, preserves black, and leaves logical hashes unchanged
- [ ] Lock effective backlight = 0 and unlock restores selected gain
- [ ] inspector resolves semantic IDs/bounds from projector metadata rather than pixels/OCR
- [ ] inspector overlay does not change framebuffer hashes
- [ ] compact bug reference format is deterministic

## Human acceptance

- [ ] dark shell is comfortable/readable on Debian desktop
- [ ] at 300% scale the 240×240 LCD presents as 720×720 without smoothing
- [ ] all required controls can be operated without physical hardware
- [ ] bug can be cited unambiguously using `screen=<id> element=<id>` from inspector

## Forbidden scope

- real backend connection
- web/JavaScript frontend
- product LCD GPIO/SPI adapter
- new UX redesign beyond baseline

## Rollback / rebuild point

If SDL shell becomes coupled to product logic, retain pure-core tests and rebuild `platform/desktop` independently.

