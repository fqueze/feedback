## Question: was this failure the test's own, or one job where everything failed?

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_ReadHeapSnapshot_worker.js --task-ids --limit 0`
- Expected: some sign, next to the task ID, that task PyUxuOpdQj2b3T3XyQOKpg had 1152 TIMEOUTs of 1552 tests (a job-wide collapse), since that decides the diagnosis on its own.
- Got: the task ID and config only; it took `fx-tests task <id>` to see the job's outcome line. The same holds for other msix jobs (XfKT04wZR42kSNwV6wuRpQ: 1375 TIMEOUT; Xie8B_H8QGiKS-RP-alnAg: 499 TIMEOUT), which inflate every test in those chunks.
- Workaround: `fx-tests task <id>` per task ID. A "job-wide: N of M tests failed" note per task ID in `fx-tests test --task-ids`, or a verdict excluding such jobs, would answer it directly.
