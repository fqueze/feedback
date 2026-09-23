## `intermittent --bug` summarises failure messages from only the jobs that have a TEST-UNEXPECTED line

- Command: `fx-tests intermittent --bug 2069174 --since 30 --tree all`
- Expected: the "Failure messages, per annotated job" and "Tests named" sections to account for all 50 annotated jobs, e.g. "49x (no TEST-UNEXPECTED line: harness error)".
- Got: `1x expected PASS` and `1x ElementHandle specs ElementHandle.click should return Point data`, with nothing about the other 49. That one job is a mis-star (bug 2072207); the bug's real failure is a Python traceback (`TypeError: '>' not supported ...`) from `mach puppeteer-test`, which has no TEST-UNEXPECTED line. Read alone, the summary says this bug is about an ElementHandle test.
- Workaround: downloaded each job's `live_backing.log` and grepped for the traceback.
- Question the default output could have answered: "which annotated jobs are this bug's own failure, and which are other tests starred on it". Printing the count of jobs with no failure line, or the job's last error line when there is no TEST-UNEXPECTED line, would have answered it.

## `intermittent` occurrence rows give push time, not job start time

- Command: `fx-tests intermittent --bug 2069174 --since 30 --tree all --limit 0`
- Expected: when each failure happened, to date the start and end of an outage-like episode.
- Got: push time only. Retriggers of one old push show that push's time, hours before they ran (for example pushes at 2026-09-03 20:04 whose jobs ran at 2026-09-04 07:30).
- Workaround: Treeherder `jobs/?job_type_id=...` API for `start_timestamp`.
