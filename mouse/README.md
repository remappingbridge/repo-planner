# Mouse project planning namespaces

This directory separates planning/execution history for the three implementation responsibilities and their shared boundary.

| Path | Responsibility | Repository |
|---|---|---|
| `ui/` | frontend/UX exploration and verification | `remappingbridge/mouse-ui` |
| `core/` | backend/Core work | `remappingbridge/mouse-core` |
| `contracts/` | UI↔Core contract proposals/promotion work | `remappingbridge/mouse/contracts` |
| `integration/` | final product composition/release work | `remappingbridge/mouse` |

## Current state

The UI reconstruction program **MUI-00 through MUI-08 is complete and ACCEPTED**. The accepted C11/SDL2 frontend baseline is `remappingbridge/mouse-ui@537b0f6fdd188b283cf10648b1cc6dbdacbfe20d`, preserved by `baseline/mui-08-accepted`.

`mouse/core/` and `mouse/integration/` remain idle. The next recommended product phase is UI↔Core contract promotion planning under `mouse/contracts/`, using the accepted MUI-08 semantics as input rather than frontend-private C types.

Legacy Mouse Bridge Remapper planning remains under the existing `mouse-bridge-remapper/` tree. It is historical source material and is not automatically active planning for the new split repositories.
