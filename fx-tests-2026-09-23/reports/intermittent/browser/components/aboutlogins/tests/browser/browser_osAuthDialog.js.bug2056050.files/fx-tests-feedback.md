## No way to see which pushes a test passed on, to bracket the landing that stopped it
- Question: the test's last failure was on autoland push 279487; two candidate fixes landed in pushes 279489 and 279499. On how many runs did it pass on the pushes in between?
- Commands: `fx-tests test <path> --history`, `--coverage --since 6`, `--task-ids --limit 0`.
- Expected: some per-revision (or per-push) pass/fail count for the test, at least on the days around a step change.
- Got: per-day counts and failing task IDs (with their revision via `fx-tests task`), nothing about passing runs' revisions. So "the landing that stopped it" (asked for by the brief) can only be bracketed from the failing side.
- Workaround: none within fx-tests; listed autoland pushes via hg json-pushes and argued from the code.
