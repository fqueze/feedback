## `fx-tests task` misses wpt variant failures (URLs with `?query`)

- Command: `fx-tests task IimXDwALRwyG9cxS4IwInw`
- Expected: the job's `TEST-UNEXPECTED-FAIL | /css/css-borders/corner-shape/render-corner-shape.html?corner-shape=-2&border-radius=20%&border-width=20` listed under FAILED.
- Got: "5795 tests, 5795 executions, 0 failing ... No test-level failure in this job." Same for `WZ4OJ8iwRS-Q2qccT3n43g`: 12 failures listed, the render-corner-shape variant failure (`?corner-shape=2.3&border-radius=40%`) is not among them.
- Workaround: downloaded `public/logs/live_backing.log` and grepped it.

## `fx-tests test` has no web-platform-tests data

- Command: `fx-tests test testing/web-platform/tests/css/css-borders/corner-shape/render-corner-shape.html` (also `--history`)
- Expected: rates per config and a history, or a message saying wpt is not covered.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...". Accurate, but I had to work out that wpt is out of scope. With `intermittent --bug`, there is no run count (so no failure rate) and no history of unstarred failures.
- Workaround: `fx-tests intermittent --bug 2070424 --history`, which counts annotations only.

## Question: "which variant/message failed in each of these tasks?"

- Command: `fx-tests intermittent --bug 2070424 --since 21 --limit 0 --full-messages`
- The text output lists failure messages with counts, and occurrences with task ids, but does not link them. I needed a script over `--json` `occurrenceRows[].lines` to get, for example, "the tasks where variant `?corner-shape=-2&...` failed", and "exactly one variant per job".
- It could have shown: the failing test (the subtest/variant for wpt) on each occurrence row, or `--message <text>` to filter occurrences.
