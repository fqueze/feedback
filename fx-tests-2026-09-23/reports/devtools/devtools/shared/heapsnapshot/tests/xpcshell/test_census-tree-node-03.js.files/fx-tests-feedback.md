## Question: "why did this xpcshell TIMEOUT happen — did the test even start?"
- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census-tree-node-03.js`
- Expected: the Windows failure identified as a launch failure / job-wide collapse (job PyUxuOpdQj2b3T3XyQOKpg had 1,152 TIMEOUTs of 1,552 tests).
- Got: `TIMEOUT Test exceeded time limit`, the same as a genuine hang. The tell (a `will retry` INFO 19 ms after the test starts, a full log holding only the "force-killed" line, a `PermissionError: [WinError 5]` traceback at the end of the job) only shows up in the resource-usage profile.
- Could have shown: "job had N TIMEOUTs of M tests" next to each failing task in `--task-ids`, so a job-wide collapse is obvious without running `fx-tests task` on each.
- Workaround: `fx-tests task <id>` plus profiler-cli.

## Question: "what is the Android failure's message?"
- Command: `fx-tests test <path>` / `fx-tests task JDhn_6FbQ0Gh5xMKGSPD6w --messages`
- Expected: `remotexpcshelltests.py | Failed to start process: … | Could not kill left-over process`.
- Got: `Failure details not recorded (likely Android or platform logging issue)`, and no message under the task row. The line is an INFO marker in the resource-usage profile, so the data exists.
- Workaround: profiler-cli `thread markers --search <test>`.

## Question: "which other jobs on this config collapsed the same way?"
- Command: none answers it directly. I used `fx-tests test <some other collapsed test> --task-ids --limit 0` (a 150-line dump that was mostly an unrelated macOS crash), then `fx-tests task <id>` on each msix task.
- Could have shown: a per-config list of jobs whose failing-test count is an outlier (for example >50% of the tests TIMEOUT).
