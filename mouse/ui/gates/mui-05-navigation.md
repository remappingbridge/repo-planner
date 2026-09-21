# MUI-05 — Navigation & Baseline UX Flows

Status: **NOT STARTED**.

## Objective

Implement the complete platform-independent MBR-08 frontend flow over the projector, interaction engine, and mock world.

## Dependencies

MUI-04 ACCEPTED.

## Tasks

1. Implement HOME resolver presentation for no-saved/live/no-live states.
2. Implement first-use, saved-search, retry, Pair New, and retry Pair New UX using deterministic mock timing.
3. Implement Help ownership/return and deadline preservation behavior.
4. Implement Lock/unlock semantics including consumed unlock interaction.
5. Implement connected HOME four-option navigation.
6. Implement Passthrough/Standard/Escape profile selection, pending/confirmed presentation, and Escape screen shortcut.
7. Implement Custom editor navigation, persistent mock draft, apply confirmation, and shared global-template presentation semantics.
8. Implement Saved Devices paging/status/connected color/removal flows.
9. Preserve page ownership on background connection changes where baseline specifies it.
10. Add end-to-end scenario tests for happy, cancel, timeout, reconnect, stale result, and operation failure paths.

## Deliverables

- complete headless frontend app/navigation state machine
- scenario regression tests for all baseline screen families
- documented semantic intents/results exercised by mocks

## Automated acceptance

- [ ] all baseline screens reachable through intended navigation
- [ ] release-triggered actions and stale epoch behavior hold end-to-end
- [ ] 8 s/15 s mock deadlines behave deterministically
- [ ] Help/Lock behavior matches active specs
- [ ] profile/Custom/removal UI distinguishes request from confirmed result
- [ ] background state changes do not steal pages contrary to spec

## Human acceptance

- [ ] manual keyboard-like scripted navigation review using a headless trace or minimal harness
- [ ] confirm flows remain understandable before desktop shell work

## Forbidden scope

- SDL desktop shell
- new UX redesign
- Core contract release
- real hardware behavior

## Rollback / rebuild point

Scenario tests define accepted behavior. Rebuild the navigation reducer if future fixes require widespread special-case patches.

