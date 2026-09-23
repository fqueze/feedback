## `fx-tests test <path> --bugs` prints nothing about bugs, and misses the job-level bug the failing jobs are starred on

- Command: `fx-tests test devtools/server/tests/xpcshell/test_nesting-04.js --bugs`
- Expected: a Bugs section, even if it says "no bug names this test"; ideally also the bugs sheriffs starred this test's failing jobs on.
- Got: the same output as without `--bugs`, no Bugs section at all, so "no bug" and "flag ignored" look the same.
- Actual situation: every failing job of this test is a job-wide breakdown (500-1500 tests failing) starred on bug 1991833, which never names the test. I found it only through a Bugzilla quicksearch on the harness traceback text.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=...`, then `fx-tests intermittent --bug 1991833`, which lists LKys8j8iTQKTAN9hKv040g among its occurrences.

## `fx-tests task` does not flag a job-wide breakdown

- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Got: "550 tests, 558 executions, 488 failing" and then 488 entries, one per test.
- Could have shown: "job-wide failure: 486 tests failed within 3 s of each other starting at t=2m12s", plus the first failure. That is the first thing to know when a test's failure is one of 488 in its job, and it took me a resource-profile load and a script to find it.
