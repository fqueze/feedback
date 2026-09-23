## `test --bugs` prints no bug section (test_close.js.desktop-shutdown)

- Command: `fx-tests test dom/webtransport/test/xpcshell/test_close.js --bugs`
- Expected: a list of bugs naming the test; bug 2070388 ("... dom/webtransport/test/xpcshell/test_close.js | single tracking bug") and bug 1982953 both name it.
- Got: the same output as without `--bugs`: no bug section, no message saying none were found.
- Workaround: Bugzilla REST quicksearch with curl.

## Question: "where does this test rank among all tests with this crash signature?"

- Command: `fx-tests crashes --signature "child process hang at shutdown" --limit 0 --json`
- Expected: all 503 tests behind the signature, so I can tell whether 68 crashes for my test is typical or an outlier.
- Got: `testCount: 503`, but the `tests` array holds only 50 entries, even with `--limit 0`. The text output shows no tests at all.
- Workaround: compared against the top 50 only (median 114). A per-test list, or a "rank of <path>" line, would answer it.

## Question: "is this crash signature job-wide in this job?"

- Command: `fx-tests task <taskId>` on each hang job, one by one.
- Expected: `fx-tests test <path> --task-ids --issue 5` saying, per task, how many other tests in the same job had the same signature. That separates "this test hangs" from "the machine hangs every child process".
- Got: I had to run `fx-tests task` on each job and read the FAILED count (17 to 146 per job).
- Workaround: sampled 7 of 68 jobs.

## `--task-ids --issue <n>` repeats the full test summary

- Command: `fx-tests test <path> --task-ids --limit 0 --issue 5` (and 6, 8)
- Got: the verdict, config table and issues list printed again before each task list. Minor: noise when you run it once per issue.
