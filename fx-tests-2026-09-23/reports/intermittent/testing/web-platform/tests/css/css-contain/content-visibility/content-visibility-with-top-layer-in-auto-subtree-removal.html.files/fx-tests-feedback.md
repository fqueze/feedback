## WPT tests are invisible to `fx-tests test`

- Command: `fx-tests test testing/web-platform/tests/css/css-contain/content-visibility/content-visibility-with-top-layer-in-auto-subtree-removal.html` (also with `--history`)
- Expected: rates per config, history, failing task ids, as for mochitest/xpcshell.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...".
- Workaround: `fx-tests intermittent --bug 1946077 --limit 0` for the starred jobs, then Treeherder `/api/jobs/?push_id=` over ~250 autoland pushes plus Taskcluster `wpt_errorsummary.log` / `wptreport.json` per job, scripted in Python.

## Question: "on which jobs did this test run, and did it pass there" (for a WPT test)

- Needed to tell apart the jobs where the test ran and passed from the jobs where it did not run, before and after a fix landed. The bug view lists only the starred failures.
- Command used instead: Treeherder job list per push, then each job's `wpt_errorsummary.log` (its `test_groups` line says whether the directory ran) and `wptreport.json` (per-test status).
- What would have answered it: `fx-tests test <wpt path> --coverage` / `--executions`, including the path-restricted (`MOZHARNESS_TEST_PATHS`) single-chunk jobs.

## Question: "in which position of the job did this test's group run"

- The failure depended on whether the test's group was the 1st or 2nd group of the job (the browsers launched at SUITE-START). Nothing in `fx-tests intermittent --bug` or `fx-tests task` shows the group order or which browser instance a group ran in.
- Command used instead: parse `test_groups` from each `wpt_errorsummary.log`, and "Application command" / "Restarting browser for new test group" lines from `live_backing.log`.
- What would have answered it: `fx-tests task <id>` listing the groups in run order with the browser instance (launch time) each used.

## `fx-tests task` on a WPT job

- Command: `fx-tests task NWS4a6B_T-ym18qk29OYAQ --profiles`
- Got: "223 tests, 223 executions" while the job log says "SUITE-START | Running 313 tests", plus a warning that 22 failing markers named no test path (they are `render-corner-shape.html?...` variants, which do have a path, only with a query string).
