# UI planning namespace

Planning home for `remappingbridge/mouse-ui`.

## Active program

The first implementation program is **MUI-00 through MUI-08**: reconstruct the MBR-08-derived frontend baseline as a clean C11 application with an SDL2 Debian desktop laboratory and no real backend dependency.

Start with [`roadmap.md`](roadmap.md) and [`status.md`](status.md). Individual gate specifications live under [`gates/`](gates/).

## Process ownership

- canonical technical/UX rules: `remappingbridge/mouse-ui/docs/`;
- gates/tasks/execution evidence: this planner directory;
- shared UI↔Core contract drafts/releases: `remappingbridge/mouse/contracts/ui-core/`;
- backend work: `repo-planner/mouse/core/` (still idle).

## Branch model after baseline reconstruction

- `lab/*` — disposable UX experiments;
- `candidate/*` — clean implementation of accepted UX;
- `rebuild/*` — clean replacement when an implementation area becomes unhealthy.

`main` is expected to remain runnable, documented, and green.

## Historical baseline

`history/mbr08-integrated-candidate-evidence.md` preserves predecessor MBR-08 integrated evidence. It is provenance, not an active gate.
