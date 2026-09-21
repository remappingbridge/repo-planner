# MUI-04 — Screen Projection & Inspectable Elements

Status: **NOT STARTED**.

## Objective

Reproduce all 30 MBR-08 baseline screens as semantic projections with dynamic fields, stable inspectable element IDs, logical bounds, and readable golden tests.

## Dependencies

MUI-01, MUI-02, and MUI-03 ACCEPTED.

## Tasks

1. Define the 30 baseline screen IDs/families from active `mouse-ui/docs/spec`.
2. Implement screen-family definitions and data-driven rows where practical; avoid a monolithic screen-specific conditional chain.
3. Project exact baseline literals/dynamic name/profile/status values and semantic tones.
4. Implement HOME 15-character + conditional standalone `MOUSE` suffix rule and Saved Devices 21-character rule.
5. Define stable semantic element IDs/roles for meaningful titles/options/status/hints/custom rows.
6. Attach logical 240×240 bounds and state/tone metadata to inspectable elements before rendering.
7. Implement semantic golden format readable in text diffs.
8. Assert element IDs are unique within a projected screen and stable across selection/tone changes.
9. Render all projected frames through MUI-01 and record deterministic framebuffer hashes.

## Deliverables

- screen/projector library
- 30-screen semantic golden suite
- element metadata/ID inventory
- 30-screen renderer hash coverage

## Automated acceptance

- [ ] all 30 baseline screens project
- [ ] exact required baseline literals/dynamic rules pass
- [ ] semantic goldens pass
- [ ] inspectable elements expose stable IDs and valid logical bounds
- [ ] selected/pressed white priority and active/connected cyan baseline rules pass
- [ ] all canonical projected frames render deterministically

## Human acceptance

- [ ] review generated screen matrix/PPMs for clipping, color, full didactic backgrounds, and readability
- [ ] review element ID vocabulary for bug-report usefulness

## Forbidden scope

- end-to-end navigation
- SDL inspector UI
- UX redesign beyond ambiguity fixes
- backend implementation

## Rollback / rebuild point

Goldens and element IDs are durable. If projector design becomes condition-heavy, rebuild by screen family from MUI-01..03 rather than preserving tangled code.

