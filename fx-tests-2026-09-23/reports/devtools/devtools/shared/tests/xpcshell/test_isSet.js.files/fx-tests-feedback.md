## Question: "is this test's failure its own, or collateral from a job where everything failed?" (test_isSet.js)

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_isSet.js` (and `--task-ids`)
- Expected: some sign that all 3 msix failures were in jobs where 750–1,563 other tests also TIMEOUTed.
- Got: `Verdict: intermittent ... worst is test-windows11-64-25h2/opt-xpcshell-msix at 1.2%`, and `TIMEOUT Test exceeded time limit` under Issues. This reads like a slow test.
- Workaround: `fx-tests task <id>` for each task ID, whose header (`1060 TIMEOUT, ... 53 PASS`) shows the meltdown. `fx-tests test` could annotate each failing task with its job's failing-test count, or flag "N of M failures were in jobs with >100 failing tests".
