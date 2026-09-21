# MUI-01 — RGB565 Renderer

Status: **NOT STARTED**.

## Objective

Implement a deterministic platform-independent renderer for the 240×240 product surface, preserving the MBR-08 visual baseline without navigation/backend logic.

## Dependencies

MUI-00 ACCEPTED.

## Tasks

1. Implement canonical RGB565 framebuffer clear/write primitives.
2. Port/verify required 5×7 glyph set and doubled-pixel text geometry.
3. Implement semantic tones/palette and MBR-08 title/body/hint geometry.
4. Support full didactic dark-magenta backgrounds and ordinary hint-region background rules.
5. Keep renderer input semantic; renderer must not know screen navigation or product operations.
6. Add deterministic framebuffer hashing and optional PPM dump utility for human debugging.
7. Add renderer tests for coordinates, clipping, palette, glyphs, backgrounds, and known frames.

## Deliverables

- pure C renderer library
- renderer contract tests
- optional host PPM evidence tool
- documented RGB565/hash behavior

## Automated acceptance

- [ ] 240×240 framebuffer size/format contract passes
- [ ] canonical glyph/palette/geometry tests pass
- [ ] known semantic frames produce stable expected hashes
- [ ] renderer compiles/tests with no SDL dependency

## Human acceptance

- [ ] review representative title/body/hint/didactic PPM output for visual correctness

## Forbidden scope

- screen navigation
- mock backend state
- SDL scale/backlight
- screen-specific product logic inside renderer

## Rollback / rebuild point

Keep semantic/render tests; if renderer structure becomes screen-specific, rebuild renderer from MUI-00 plus test vectors.

