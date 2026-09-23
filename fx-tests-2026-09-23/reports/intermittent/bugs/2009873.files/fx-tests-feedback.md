## Question: "on which days did this job type run, pass, fail, or not run at all?" (snap / non-mochitest/xpcshell harness)

- Command: `fx-tests test test_pdf_navigation` (the test of bug 2009873, snap Selenium QA tests)
- Expected: per-config pass/fail history, or at least the run history of the jobs sheriffs starred on the bug.
- Got: `No test path in the xpcshell and mochitest 21-day data contains "test_pdf_navigation"`. `fx-tests intermittent --bug 2009873` lists the annotated jobs, but only for 7 days, and it cannot show passing or unscheduled runs.
- Workaround: the Treeherder API. `api/jobs/?task_id=<id>` gives the job signature, then `api/jobs/?repo=mozilla-central&signature=<sig>&last_modified__gt=<date>`, plus `api/failuresbybug/?bug=<N>&startday=...` for a longer annotation history. That is what showed the nightly job unscheduled from 2026-08-06 to 2026-09-11, which hid when the failure started.
- What could have answered it: a `--history` for `intermittent --bug`, or for a job name, showing success, testfailed and unscheduled per day, for harnesses fx-tests does not index.
