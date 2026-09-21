# MUI-08 — Clean MBR-08 Frontend Baseline

Status: **NOT STARTED**.

## Objective

Prove that the new C frontend reproduces the intended MBR-08 UX baseline, clean architectural boundaries, and desktop laboratory features; then establish the baseline from which UX exploration can safely diverge.

## Dependencies

MUI-07 ACCEPTED.

## Tasks

1. Run complete automated suite: architecture, interaction, mock, semantic projection, renderer, navigation scenarios, desktop presentation calculations, inspector/evidence contracts, sanitizers.
2. Generate/review the complete 30-screen matrix at baseline scale/backlight reference.
3. Exercise all major user-observable baseline flows through the C desktop application.
4. Compare active specs against implementation and resolve any undocumented divergences.
5. Review dependency graph for SDL/core/mock/projector/renderer leakage.
6. Review special-case density and rebuild any area that already violates clean-module criteria.
7. Record exact build toolchain, candidate commit, artifacts, hashes, known limitations, and human visual/navigation results.
8. Update active documentation only for explicitly accepted baseline clarifications.
9. Create an accepted baseline tag after approval; do not start UX redesign before baseline status is clear.

## Deliverables

- accepted C-only MBR-08 frontend baseline
- complete evidence record
- clean architecture review
- baseline executable/artifact
- stable tag/commit suitable as base for `lab/*` experiments

## Automated acceptance

- [ ] all automated tests green
- [ ] sanitizers clean where supported
- [ ] all 30 screens covered by semantic + renderer regression
- [ ] all requested scale/backlight/inspector desktop requirements verified
- [ ] no backend/SDL leakage across documented boundaries
- [ ] documentation and code inventory agree

## Human acceptance

- [ ] human review of all screen families at useful scales including 300%
- [ ] human navigation review of first use, HOME, Pair New, profiles, Custom, Saved Devices, Help, Lock
- [ ] inspector references are practical for reporting observed bugs
- [ ] dark shell/backlight/scale behavior accepted as the development laboratory baseline

## Forbidden scope

- new post-MBR-08 UX redesign
- mouse-core implementation
- public contract v1 release
- embedded ST7789 integration

## Rollback / rebuild point

If parity is achieved with unhealthy architecture, do not accept. Rebuild the offending module/gate from the latest clean accepted dependency and rerun parity.

