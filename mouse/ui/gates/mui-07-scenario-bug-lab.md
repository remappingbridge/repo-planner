# MUI-07 — Scenario, Fault & Bug Evidence Lab

Status: **NOT STARTED**.

## Objective

Make difficult UX states instantly reproducible and make bug reports self-identifying, deterministic, and useful for rebuilds.

## Dependencies

MUI-06 ACCEPTED.

## Tasks

1. Define named scenario catalog covering each major screen family and edge state.
2. Add CLI and/or shell scenario selection so relevant states can open directly without manual setup.
3. Add controlled fault/result injection: success, failure, timeout, disconnect, stale/late result, operation pending, full registry, dirty Custom draft, etc.
4. Allow deterministic event-step execution and virtual-time advancement.
5. Create bug evidence capture containing screen ID, focused/inspected element ID, logical bounds, text/state/tone, scenario, mock clock, scale, selected/effective backlight, and recent semantic event history.
6. Provide one-click/copyable compact reference plus optional structured text/JSON evidence export.
7. Include framebuffer capture/hash in evidence without making screenshots the source of truth.
8. Add regression scenarios for bugs as they are found: every fixed reproducible bug should be representable as a scenario/test where practical.
9. Document how a human reports a visual/navigation bug using inspector references.

## Deliverables

- named scenario catalog
- fault injection controls
- deterministic scenario runner
- bug evidence/reference export
- bug-reporting documentation and tests

## Automated acceptance

- [ ] named scenarios initialize deterministically
- [ ] fault injections reach expected frontend states without backend code
- [ ] same scenario/steps produce same semantic trace/framebuffer hash
- [ ] evidence export contains stable screen/element references and relevant presentation settings
- [ ] bug evidence generation does not mutate product state except explicit scenario actions

## Human acceptance

- [ ] select representative difficult states in a few interactions or directly by scenario
- [ ] copy a useful bug reference/evidence block from a visible element
- [ ] reproduce at least one intentionally introduced visual/navigation regression from saved scenario evidence

## Forbidden scope

- automatic backend diagnosis
- network/cloud telemetry
- real Bluetooth/USB fault injection
- turning developer evidence metadata into product UI

## Rollback / rebuild point

Scenario definitions, evidence format, and regression tests survive implementation rebuilds. Shell implementation may be replaced freely.

