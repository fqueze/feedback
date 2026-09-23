## Question: was this test's only failure part of a job-wide collapse?

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTreeNode_insert_01.js --task-ids`
- Expected: some hint that the single failing job (PyUxuOpdQj2b3T3XyQOKpg.0) failed 1154 of its 1552 tests, i.e. the failure says nothing about this test.
- Got: "Verdict: intermittent" and a TIMEOUT row, identical to a genuine intermittent. Only `fx-tests task <id>` revealed "1152 TIMEOUT".
- Could show: next to each task id, the job's failing-test count (or a "job-wide: N of M tests failed" flag), and a verdict like "only failures come from jobs where >50% of tests failed".
- Workaround: ran `fx-tests task` on the job.
