# MUI-02 — Interaction Engine

Status: **NOT STARTED**.

## Objective

Implement platform-independent semantic HAT press/release ownership and interaction primitives needed by baseline UX.

## Dependencies

MUI-00 ACCEPTED; may proceed independently of MUI-01.

## Tasks

1. Define JOY UP/DOWN/LEFT/RIGHT/PRESS and KEY A/B/X/Y semantic controls.
2. Implement distinct press/release events and visible held-state representation.
3. Implement interaction epoch/owner semantics so stale releases cannot act on a new screen.
4. Implement generic consumed interaction primitive for unlock/Help ownership use.
5. Add exhaustive unit tests for duplicate presses, orphan releases, epoch changes, held feedback, and consumed release behavior.

## Deliverables

- pure interaction library/API
- interaction ownership tests
- documented input semantics

## Automated acceptance

- [ ] actions trigger only on matching release
- [ ] stale release after epoch change does not trigger
- [ ] duplicate press/release edge cases are deterministic
- [ ] no SDL/GPIO dependency

## Human acceptance

- [ ] review semantic mapping naming for future desktop and embedded adapters

## Forbidden scope

- GPIO pin mapping
- SDL key mapping beyond test fixtures
- screen navigation rules
- backend operations

## Rollback / rebuild point

Interaction tests are the durable asset; rewrite implementation if ownership semantics become entangled with screen-specific conditions.

