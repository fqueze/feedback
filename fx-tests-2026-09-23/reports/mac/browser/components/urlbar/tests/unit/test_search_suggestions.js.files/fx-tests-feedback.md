## Question: "how long did this test's passing retry take, and how close was it to the timeout?"

- Command: `fx-tests task Thv1kMPaSFik-DiQ2HdezA --json` (also the text output with `--profiles`).
- Expected: per-execution duration of the failing test (the parallel TIMEOUT and the sequential
  PASS rerun), and ideally the harness timeout in effect for the job.
- Got: `failures[]` entries with `failureCount`, `executionCount`, `statuses`, `passedOnRerun`,
  `parallelOnly`, `messages` -- no durations, no timeout.
- Workaround: loaded the job's resource-usage profile in profiler-cli and read the `test`
  markers (`PASS — test_search_suggestions.js` 26.400 s, `TIMEOUT` 52.640 s).
- What would have answered it: a duration per execution in `fx-tests task` (it already reads
  the resource-usage profile, whose `test` markers carry them), plus the job's harness timeout
  ("Using harness timeout of 30s" is in the log). For a timeout, "the rerun passed in 26.4 s of
  a 30 s budget" is the whole diagnosis of a too-slow test.
