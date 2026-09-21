# Mouse project planning namespaces

This directory separates planning/execution history for the three implementation responsibilities and their shared boundary.

| Path | Responsibility | Repository |
|---|---|---|
| `ui/` | frontend/UX exploration and verification | `remappingbridge/mouse-ui` |
| `core/` | backend/Core work | `remappingbridge/mouse-core` |
| `contracts/` | UI↔Core contract proposals/promotion work | `remappingbridge/mouse/contracts` |
| `integration/` | final product composition/release work | `remappingbridge/mouse` |

## Current state

The UI program is now active as **MUI-00 through MUI-08**, documented under `mouse/ui/`. It reconstructs the MBR-08-derived frontend as a clean C11/SDL2 desktop laboratory before later UX exploration.

`mouse/core/`, `mouse/contracts/`, and `mouse/integration/` remain planning namespaces without active implementation gates at this stage.

Legacy Mouse Bridge Remapper planning remains under the existing `mouse-bridge-remapper/` tree. It is historical source material and is not automatically active planning for the new split repositories.
