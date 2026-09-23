## fx-tests test cannot read web-platform-tests

- Command: `fx-tests test /css/css-animations/animation-duration-infinite.html` (help lists `--harness <xpcshell|mochitest>` only)
- Question: how often does this wpt test fail on macosx1500-aarch64 debug, and does it pass in the other runs?
- Expected: the per-config rates and history, as for mochitest.
- Got: no wpt harness to pick.
- Workaround: listed the mac debug wpt-reftest jobs through the Treeherder API since 2026-09-16, downloaded each job's `wptreport.json` (79 ran the directory), and tallied the test's status by script. About 280 downloads and a script; the result is in `next-test-outcomes.tsv`.

## fx-tests intermittent --bug: "Failure messages" covers 2 of 32 jobs

- Command: `fx-tests intermittent --bug 2054343`
- Question: which failure line turned each annotated job orange?
- Got: "Failure messages, per annotated job": 1x a grid-lanes "Testing ..." line and 1x another one. The other 30 jobs had no line, and neither of the two was the failure behind the bug. In 31 of the 32 jobs that failure was `TEST-UNEXPECTED-ERROR | animation-duration-infinite.html | Please start a session` or its UNEXPECTED-PASS.
- Workaround: downloaded `wpt_errorsummary.log` for all 32 tasks and tallied the unexpected results by script.

## fx-tests task works for wpt jobs (positive)

- `fx-tests task <taskId> --profiles` on a wpt reftest job listed the CRASH and the next test's ERROR, which pointed straight at the victim test.
