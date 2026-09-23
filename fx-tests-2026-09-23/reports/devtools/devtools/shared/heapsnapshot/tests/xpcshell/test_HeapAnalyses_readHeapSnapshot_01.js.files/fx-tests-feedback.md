## "Failure details not recorded" hides a harness message that is in the resource-usage profile

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapAnalyses_readHeapSnapshot_01.js`
- Expected: the Android failure's message, or a pointer to it.
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)`. The job's resource-usage profile (task G3QLvkwXS-q0xVYXuLtImA) has the cause as an INFO marker attributed to the test by name: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`. `fx-tests failures --message "Failure details not recorded"` then matches nothing, so "which other tests did this harness failure hit" has no answer.
- Workaround: loaded the resource-usage profile and searched the test name.

## Question: "did this job collapse at one moment, and when?"

- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles --limit 0` (4,634 lines).
- Expected: a line saying 1,152 of 1,552 tests timed out, all starting within ~5 s of t=52 s, i.e. a job-wide collapse rather than 1,152 separate timeouts.
- Got: the per-test list only; had to script over `profiler-cli thread markers --search name:test --json` to bin test statuses by start time.
- What could show it: a per-job timeline summary (failures binned by start time) or a "job-wide collapse" verdict when most tests fail in one burst.
