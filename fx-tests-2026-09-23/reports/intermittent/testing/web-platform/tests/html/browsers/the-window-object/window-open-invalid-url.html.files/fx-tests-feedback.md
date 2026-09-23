## WPT tests are not covered by `fx-tests test`
- Command: `fx-tests test testing/web-platform/tests/html/browsers/the-window-object/window-open-invalid-url.html` (also with `--history`)
- Expected: per-config rates and history for a web-platform-test, as for mochitest/xpcshell.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...` (`--harness` only accepts xpcshell|mochitest).
- Workaround: Treeherder `api/failuresbybug/?bug=N` for the occurrences, then walking `api/jobs/?push_id=` and each task's `wpt_errorsummary.log` `groups` line to find which jobs ran the test's directory (passing runs). `fx-tests task <taskId>` and `fx-tests intermittent --bug` did work for WPT.
- Question it could have answered: "in how many linux64-debug runs of this WPT did it fail, and which job ran it on each push?"
