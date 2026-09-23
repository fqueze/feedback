## Marionette tests have no `fx-tests test` data, and the error does not point to what works

- Command: `fx-tests test netwerk/test/marionette/test_beacon_on_pagehide_shutdown.py`
- Expected: rates per config, or a pointer to the command that covers marionette tests.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...`. `fx-tests intermittent --test <path>` / `--bug <N>` did work for this test.
- Workaround: `fx-tests intermittent --bug 1989564 --limit 0 --since 21`. The error could suggest that command when the path looks like a marionette test (`.py` under `*/marionette/`).
- There were no run counts, so I got the denominators from the Treeherder jobs API by job type.

## Question: "do all failures of this bug share a log line besides the failure message?"

- Command: `fx-tests intermittent --bug 1989564 --limit 0 --json`, then a script: curl each of the 91 `live_backing.log`s and awk out the section from the test's TEST-START to `TEST-INFO took`.
- `occurrenceRows[].lines` holds only the TEST-UNEXPECTED-FAIL line. The line that decided this diagnosis was 15 lines above it (`JavaScript warning: ... Script terminated by timeout at: ...:182:35`), and it was in all 91 logs.
- What would have answered it: `--grep <text>` over the occurrences' logs, counting matches per occurrence, or `--context <n>` to print the lines before each failure line.

## `fx-tests task` on a marionette job

- Command: `fx-tests task AZRyKxBGQG23507j_CGqnA --profiles`
- Got: `0 tests, 0 executions, 0 failing` and "the harness died before it ran one". In fact the job ran about 200 tests. The resource-usage profile of a marionette job simply has no test markers.
- The output could say this job's harness does not record test markers, instead of suggesting that the harness died.
