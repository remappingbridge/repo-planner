# MUI-00 — Foundation

Status: **NOT STARTED**.

## Objective

Create the minimum clean C11 frontend architecture and testable build skeleton without implementing product screens or backend behavior.

## Dependencies

Active `mouse-ui/docs` architecture/process baseline.

## Tasks

1. Create CMake project with explicit frontend libraries/modules rather than one monolithic executable.
2. Establish pure core types: logical 240×240 RGB565 framebuffer, bounded strings/capacities, semantic control/event identifiers, explicit app/context ownership.
3. Define module dependency direction and include boundaries so SDL/backend types cannot leak into core headers.
4. Create host unit-test harness and deterministic test entry points.
5. Enable strict host warnings and Debug sanitizer configuration where supported.
6. Create CI host build/test job(s) for normal Debug plus sanitizers where practical.
7. Add architecture checks for forbidden dependencies/includes at the frontend boundary.
8. Document exact developer build/test commands.

## Deliverables

- `mouse-ui` CMake build with core/test targets.
- Empty/placeholder framebuffer path that proves module composition.
- CI and architecture tests.
- Developer build documentation.

## Automated acceptance

- [ ] clean configure/build on supported Debian/CI toolchain
- [ ] unit test harness executes successfully
- [ ] architecture guard rejects an intentional forbidden SDL/core dependency fixture or equivalent contract test
- [ ] ASan/UBSan configuration runs without findings on baseline tests where supported

## Human acceptance

- [ ] build/run instructions are understandable and reproducible on Debian
- [ ] module layout matches documented responsibility boundaries

## Forbidden scope

- SDL product shell functionality beyond a minimal compile probe
- real MBR screens/navigation
- BLE/USB/storage/Core implementation
- public UI↔Core contract release

## Rollback / rebuild point

Restart from the documentation-only baseline if dependency direction or module ownership is wrong; do not carry a bad scaffold forward.

