## Question: "are this test's failures job-wide breakdowns rather than the test?"
- Command: `fx-tests test browser/components/places/tests/unit/test_browserGlue_migrate.js` then `fx-tests task <id> --profiles` for each of the 9 jobs.
- Needed: for each failing run, how many other tests failed in the same job. All 8 TIMEOUTs were in jobs with 275–1563 timeouts each; the 2 FAILs in a job with 55 failures. `fx-tests test` shows only "8x TIMEOUT", which reads as a test-specific timeout.
- Could have shown: a per-issue "co-failures in the same job" median, or a flag like "in a job where >N% of tests failed".
- Workaround: loop `fx-tests task` over every task id.

## `--bugs` printed no bug section at all
- Command: `fx-tests test browser/components/places/tests/unit/test_browserGlue_migrate.js --bugs`
- Expected: a Bugs section, even "none naming this test", ideally also the bugs sheriffs starred these jobs on (here bug 1991833, which carries ZgUlY0ZlRvmHZNTOfia3AA and OgH9QhqjS4a-6VFFNGQrEg).
- Got: the same output as without `--bugs`.
- Workaround: Bugzilla quicksearch on the traceback text found bug 1991833.
