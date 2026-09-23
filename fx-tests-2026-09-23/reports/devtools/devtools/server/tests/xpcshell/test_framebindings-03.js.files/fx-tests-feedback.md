## Question: "is this test's failure its own, or a job-wide breakage?"
- Command: `fx-tests test devtools/server/tests/xpcshell/test_framebindings-03.js`
- Expected: some hint that all 6 failing jobs had hundreds to thousands of other tests failing too (e.g. "each failing job had 477-1564 failing tests").
- Got: "Verdict: intermittent", "6x TIMEOUT Test exceeded time limit". Nothing suggests the job itself broke. I only found out with `fx-tests task <id>` on each job ("550 tests, 488 failing").
- Workaround: ran `fx-tests task` on all 6 jobs and read their headers.

## Question: "where is my test in this job's output?"
- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Expected: a way to see the entry for one test, e.g. `--test <path>`.
- Got: the first ~20 of 488 failing tests, alphabetically. My test was not among them.
- Workaround: `--limit 0` into a file, then rg (1,969 to 6,270 lines per job).

## Question: "which bug covers this failure?"
- Command: `fx-tests test <path> --bugs`
- Expected: bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"). Sheriffs starred LKys8j8iTQKTAN9hKv040g, one of this test's failing jobs, on it.
- Got: no bug. The bug names no test, so a match on the test name misses it.
- Workaround: searched Bugzilla by hand for the traceback text. `fx-tests intermittent --bug 1991833` then confirmed the task. Matching this test's failing task IDs against annotated task IDs would have found it.

## The TIMEOUTs are launch failures
- `fx-tests test` reports "TIMEOUT Test exceeded time limit". The test never started: CreateProcess raised PermissionError and the harness's kill timer fired 45 s later. Counting these as timeouts sends people to the test. The "Access is denied" traceback is in each job's log (in the retry phase), so a job-level flag would be possible.
