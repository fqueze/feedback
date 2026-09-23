## Question: did my test fail alone, or did the whole job collapse around it? (test_objectgrips-19.js)

- Command: `fx-tests test devtools/server/tests/xpcshell/test_objectgrips-19.js --task-ids --limit 0`
- Expected: some hint, per failing task, of how many other tests failed in the same job.
- Got: 6 task IDs and "6x TIMEOUT Test exceeded time limit". Only by running `fx-tests task <id>` on each did it appear that every one of the 6 jobs had 476-1,563 other TIMEOUTs (a job-wide collapse, bug 1991833), which is the whole diagnosis.
- Could show: next to each task ID, "N other tests failed in this job" (the data `fx-tests task` already reads), or a verdict line such as "all failing runs are in jobs where >50% of tests failed".

## Question: which bug are my test's failing jobs starred with?

- Command: `fx-tests test devtools/server/tests/xpcshell/test_objectgrips-19.js --bugs`
- Expected: bug 1991833, which sheriffs starred on these jobs (LKys8j8iTQKTAN9hKv040g is one of its annotations).
- Got: no bugs section, since no bug names the test.
- Could show: the bugs the failing tasks were annotated with, even when they name no test.

## Question: how did my test end in this one job?

- Command: `fx-tests task <taskId> --profiles` (default limit 20 rows, alphabetical)
- Expected: a way to see one test's row in a job with ~500 failures.
- Got: had to rerun with `--limit 0 --full-messages --messages` and grep the 2,000-6,000 line output.
- Workaround: grep. Could show: a `--test <path>` filter on `fx-tests task`.
