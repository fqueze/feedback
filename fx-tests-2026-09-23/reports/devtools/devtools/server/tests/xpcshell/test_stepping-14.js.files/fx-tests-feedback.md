## Question: "was this test's failure part of a mass failure of the whole job?"

- Command: `fx-tests test devtools/server/tests/xpcshell/test_stepping-14.js --task-ids --limit 0`
- Expected: some hint that each of the 6 failing jobs had 477–1,564 other tests fail the same way (all 6 are job-wide collapses in which the test never started).
- Got: a plain list of task IDs; the verdict reads "intermittent … 1.8%" as if the test itself were flaky. Only running `fx-tests task <id>` on each job revealed `Outcomes: 487 TIMEOUT, 41 PASS…`.
- Workaround: `fx-tests task <id>` per job.
- What the output could show: per task ID, the number of other failing tests in that job (e.g. `LKys… (job: 487 other failures)`), and a verdict line when most of a test's failures come from such jobs ("6/6 failures are in jobs where >400 tests failed").
