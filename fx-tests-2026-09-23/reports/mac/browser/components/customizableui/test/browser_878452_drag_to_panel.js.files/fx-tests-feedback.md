# fx-tests feedback (browser_878452_drag_to_panel.js)

## Question: how many times did this test run and fail on the try push?

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids`
- Expected: the test's failures and runs on the push.
- Got: `3  browser/.../browser_878452_drag_to_panel.js  3/7`, 3 tasks on 2 configs. The documented default reads only failed jobs, but "3/7" reads as the whole answer. `--all-jobs --test <path>` showed 6 jobs on 3 configs (debug-mochitest-browser-chrome-9 included), all 6 "passed on retry", i.e. 6 failures in 12 executions. What the 7 in "3/7" counts is not clear.
- Workaround: `fx-tests try <rev> --all-jobs --test <path>`.

## Question: the task IDs of the jobs in the `--test` table

- Command: `fx-tests try <rev> --all-jobs --test <path> --task-ids`
- Expected: task IDs per configuration row.
- Got: the same table with no task IDs.
- Workaround: `fx-tests try <rev> --all-jobs --task-ids --full-messages` for the whole push, then grep the test (slow and large).

## "This exact failure was never seen in history — it looks new."

- Command: `fx-tests try 9db28f6db8bf... --profiles --task-ids`
- Got: that line for `uncaught exception - TypeError: can't access property "dataTransfer", sess is null`, while `fx-tests test <path>` shows that exact message as the first failure of 8932 central runs in the same window, on Linux and Windows. The line is only true for the push's (macOS) configurations, which central skips; said without that qualifier, it points at the patch.
