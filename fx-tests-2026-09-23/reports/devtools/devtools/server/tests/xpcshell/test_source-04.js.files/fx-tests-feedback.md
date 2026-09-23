# fx-tests feedback (test_source-04.js)

## Question: "were these failures part of a whole-job collapse?"
- Command: `fx-tests test devtools/server/tests/xpcshell/test_source-04.js --task-ids --limit 0`
- Expected: some hint, per failing task, of how many other tests failed in the same job.
- Got: 6 TIMEOUTs listed as "intermittent ... TIMEOUT Test exceeded time limit", nothing saying that in every one of those jobs 476-1,563 other tests also timed out. I had to run `fx-tests task` on each of the 6 tasks to find out.
- Could have shown: an "other failures in job" count next to each task ID, or a verdict line such as "all 6 failures are in jobs where >400 tests failed: job collapse, not this test".

## TIMEOUT reported for tests that never launched
- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Expected: tests whose process could not be launched (PermissionError in CreateProcess, the harness's "Following exceptions were raised" traceback) not to be counted as TIMEOUT.
- Got: "487 TIMEOUT", and every row says "Timed out and was force-killed by the harness". 480 of them started within 2 s and all lasted 45.0 s: that is the harness's kill timer firing for processes that never started.
- Workaround: loaded the resource-usage profile and read the harness traceback at the end of the run.
- Could have shown: a job-level warning when hundreds of TIMEOUTs share the same start second and duration, plus the harness exception text if there is one.

## `--bugs` prints nothing when no bug is found
- Command: `fx-tests test devtools/server/tests/xpcshell/test_source-04.js --bugs`
- Expected: a "Bugs: none" line.
- Got: the normal output with no bugs section at all, so I could not tell "no bug" from "the lookup did not run".
