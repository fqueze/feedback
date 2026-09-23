# fx-tests feedback (bug 2016044, xpcshell self-test failures on msix)

## `fx-tests task` calls a job that failed in the xpcshell self-tests "no tests at all"

- Command: `fx-tests task fll3yaUKQxOZYu54JR8v0g --profiles`
- Expected: that the job failed in the harness self-tests (`xpcshell-selftest` group, 40 of 64
  failed), and that the real suite therefore never ran.
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all. Either the job
  is not a test job, or the harness died before it ran one". The resource-usage profile does hold
  65 `test` markers (the self-tests, with PASS/FAIL status). The warning above the table did name
  them ("40 failing markers in this job named no test path"), but the verdict contradicts it.
- Workaround: read the log and the profile's `test` markers directly.

## Question: how many jobs of one config failed with one message that names no test path?

- Question: "how many of this week's `xpcshell-msix` jobs failed in the self-tests with
  `WinError ... 0xc007001a`, annotated or not?"
- `fx-tests test testDoReportForeignObject` exits 2 (self-tests are not test paths), and
  `fx-tests intermittent --bug 2016044` only counts the 18 jobs sheriffs starred on this bug. The
  same failure was also starred on bugs 1988722 and 2016008, and 47 more were left unclassified.
- Workaround: Treeherder `/api/jobs/?job_type_name=...` for the 521 finished jobs, then the
  `xpcshell_errorsummary.log` of the 139 failed ones, grepped: 87 matched.
- What could have shown it: `fx-tests failures --message <text> --config <job>` counting jobs
  (not tests) whose error summary holds the message, including harness-level groups such as
  `xpcshell-selftest`.
