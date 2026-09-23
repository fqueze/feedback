## One test's entry in a job with 1000+ failing tests

- Question: how did this test fail in this job, and does it have a per-test profile?
- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles`
- Expected: a way to ask for one test, e.g. `--test <path>`.
- Got: the first ~20 of 1061 failing tests and `… 1041 more (--limit 0 for all)`.
- Workaround: `--limit 0 | grep -A3 <test path>`.

## The same failure shows two different messages

- Command: `fx-tests task <id> --profiles --limit 0` for the six msix jobs of `devtools/server/tests/xpcshell/test_new_source-01.js`.
- Got: `Test timed out` for four jobs and `... | Timed out and was force-killed by the harness; ...` for two, although each of the six profiles has both (the `test` marker's message and the ERROR), in the same order.
- Expected: the same message picked for the same sequence, so failures do not look like two modes.
