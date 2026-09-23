## Question: was this test's failure part of a whole-job breakdown?

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapSnapshot_deepStack_01.js --task-ids`
- Expected: next to each failing task, how many other tests failed in that job (here 1,153 of 1,552 timed out in PyUxuOpdQj2b3T3XyQOKpg), so a failure that is one row of a mass breakdown is recognisable without opening the job.
- Got: task IDs only; it took `fx-tests task <id> --profiles` (4,600 lines with `--limit 0`) to see the job had collapsed.
- Workaround: `fx-tests task <id>` header line "1154 failing".

## Question: what did the Android failure actually say?

- Command: `fx-tests test <path>` (Issues) and `fx-tests task PPmLxeNZRiCUEAN99nM5Vg --profiles`
- Expected: the harness's own message for the failing execution, which the job's resource-usage profile holds as an INFO marker: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`.
- Got: `FAIL    Failure details not recorded (likely Android or platform logging issue)`, and no message in the task view.
- Workaround: loading the resource-usage profile and `thread markers --search <test file name>`.
