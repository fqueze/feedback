## Question: "since when does this test really fail?" (a harness attribution change looks like a regression)
- Command: `fx-tests test toolkit/content/tests/widgets/test_panel_list_submenu_click.html --history`
- Expected: timeouts from the day the test landed (2026-09-08), since it hung the same way from the start.
- Got: 0 timeouts until 2026-09-14, then about 150 a day. Before bug 2062615 (landed 2026-09-14), `TestRunner._checkForHangs` filed the 4th timeout of a run under `(SimpleTest/TestRunner.js)`. So a pre-09-14 job (IIas1LD0Qi2dUSg6KXKF_Q) lists this test as `PASS, 1 execution`, counting only the retry, although its first run timed out. Only the job's resource-usage profile showed that.
- Suggestion: count `(SimpleTest/TestRunner.js)` results, which name the test URL in their message ("finished in a non-clean fashion … <url>"), against that test. At least flag days before 2026-09-14 where a test's only execution in a job is a harness rerun.

## Question: "which revision did each failing task run on?" (to date the start and find the landing)
- Command: `fx-tests test <path> --task-ids --issue 1 --day 2026-09-14 --limit 0 --json`
- Expected: a revision (or push time) per task, so the first failing push can be found.
- Got: taskId, jobName, day and status only. Getting the revision takes one `fx-tests task <id>` per task.
- Workaround: ran `fx-tests task` on single tasks.
