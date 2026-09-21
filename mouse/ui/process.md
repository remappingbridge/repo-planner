# Execution process

## Gate branches

For the reconstruction program, prefer one focused branch per gate, for example `mui/mui-00-foundation`. A gate branch starts from the latest accepted dependency baseline.

## Candidate evidence

Each executed gate should create an evidence directory under `executions/<gate>/` containing at least:

- candidate branch and exact commit;
- implemented task checklist;
- automated test/build commands and results;
- artifacts/hashes where relevant;
- known limitations;
- human test scenarios/results where required;
- rollback base.

## Cleanliness rule

If a gate implementation accumulates architecture violations or extensive workaround conditionals, do not preserve it merely because behavior appears correct. Keep specifications/tests, abandon the implementation branch, and use a `rebuild/*` or fresh gate candidate.

## Baseline tags

After major accepted checkpoints, create a stable tag so later `lab/*` experiments can branch from a known clean frontend. Exact tag names are chosen when those checkpoints are accepted.

## No backend claims

Mock results and frontend test PASS never imply BLE/USB/flash/backend functionality. Those belong to separate Core/integration programs.
