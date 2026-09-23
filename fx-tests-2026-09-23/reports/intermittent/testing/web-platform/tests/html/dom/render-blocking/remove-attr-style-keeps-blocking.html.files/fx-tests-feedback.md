## `fx-tests task` omits a wpt subtest failure from its FAILED list

- Command: `fx-tests task Z-0Zwdt0TtOHA4fmqoYjZA --profiles`
- Expected: `/html/dom/render-blocking/remove-attr-style-keeps-blocking.html` listed under FAILED. The job log has `TEST-UNEXPECTED-FAIL | /html/dom/render-blocking/remove-attr-style-keeps-blocking.html | Rendering is blocked before ... assert_greater_than ...`, and sheriffs starred bug 2033204 on this job.
- Got: `FAILED (4)` lists four other tests (ERROR status), not this one. The test-level status was OK and only a subtest FAILed, so it seems subtest-level unexpected results are dropped.
- Workaround: grep the job's `live_backing.log` / `wptreport.json`.

## Question: "how often does this wpt test fail, per config?"

- Command: `fx-tests test testing/web-platform/tests/html/dom/render-blocking/remove-attr-style-keeps-blocking.html` (and `--history`)
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...". No wpt data, so no run count or failure rate, only sheriff annotations from `fx-tests intermittent --bug`.
- Workaround: sheriff annotation counts only. For run counts I scripted Treeherder jobs plus each task's `wpt_instruments.txt` and `wpt_errorsummary.log`: slow, since there are about 40 macOS wpt jobs per push.
- What would have answered it: wpt in `fx-tests test`, with subtest-level unexpected results.

## Question: "the numbers in each failure message, per occurrence"

- Command: `fx-tests intermittent --bug 2033204 --since 90 --full-messages`
- Got: messages grouped by exact text, 46 near-unique rows for one failure mode, since each embeds two timestamps. To get the FCP/load gap distribution I had to parse the text with a script.
- What would have answered it: an option to group messages with the numbers masked (e.g. `--normalize-numbers`), showing the number ranges.
