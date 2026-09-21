# MUI-00 — Foundation

Status: **AUTOMATED PASS / HUMAN ACCEPTANCE PENDING**.

Candidate: `remappingbridge/mouse-ui` branch `mui/mui-00-foundation`, commit `f0007cb7f5c19543238b564b0295256e57fec3aa`.

Evidence: `../executions/mui-00/candidate.md`.

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

- [x] clean configure/build on supported Debian/CI toolchain
- [x] unit test harness executes successfully
- [x] architecture guard rejects an intentional forbidden SDL/core dependency fixture or equivalent contract test
- [x] ASan/UBSan configuration runs without findings on baseline tests where supported

Automated evidence: GitHub Actions run `35565090706`, `host-debug` and `host-asan-ubsan` both successful; 3/3 CTest contracts passed in both jobs.

## Human acceptance

- [ ] build/run instructions are understandable and reproducible on Debian
- [ ] module layout matches documented responsibility boundaries

Human acceptance is intentionally not inferred from CI or agent-side local execution.

## Forbidden scope

- SDL product shell functionality beyond a minimal compile probe
- real MBR screens/navigation
- BLE/USB/storage/Core implementation
- public UI↔Core contract release

Candidate inspection confirms none of the forbidden product/backend scopes were introduced. SDL appears only in the intentional negative architecture fixture and documentation; no SDL product shell exists.

## Rollback / rebuild point

Documentation-only baseline and branch base: `mouse-ui@ecfe89918813269783269a66734cdf0aa29a0539`. Restart from that commit if the foundation is rejected rather than carrying a bad scaffold forward.
