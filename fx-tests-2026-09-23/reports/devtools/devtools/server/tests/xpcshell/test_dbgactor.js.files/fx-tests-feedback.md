## `fx-tests test <path> --bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/server/tests/xpcshell/test_dbgactor.js --bugs`
- Expected: a "Bugs" section, even if it says "no bug names this test" — and ideally the bugs the failing *jobs* were starred with.
- Got: exactly the same output as without `--bugs`, no Bugs section, no "none found" line. I could not tell whether the lookup ran.
- Workaround: Bugzilla REST search on `summary=msix`, which found bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"): the bug these 6 jobs belong to. The test's failures are fallout of a job-wide collapse whose bug names no test.

## Question: "is this test's failure part of a job-wide collapse, and what message is its cause?"

- Command: `fx-tests test <path>` then `fx-tests task <taskId>` for each of the 6 jobs.
- Got: `fx-tests test` lists the failure as `TIMEOUT Test exceeded time limit`; only by opening each job did I see 477-1563 of ~550-2100 tests timing out, and the job-level `PermissionError: [WinError 5] Access is denied` traceback (logged outside any test) is not shown anywhere in fx-tests.
- Could have shown: in `fx-tests test`, per failing job, the fraction of the job's tests that failed ("488/550 failed in this job"), flagging jobs where nearly everything failed as a job-wide event; and in `fx-tests task`, the harness-level ERROR lines not attributed to a test.
