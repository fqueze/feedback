## Question: which test was running when mozharness killed the job?

- Command: `fx-tests task UKBqeZpIT1e3eWPVYHBCmA --profiles`
- Expected: for a job killed by "Automation Error: mozharness timed out after 1000 seconds", the test that had a `TEST-START` and no end (here `dom/tests/browser/browser_noopener.js`, the same test in 14 of the bug's 19 jobs).
- Got: "300 tests, 300 executions, 0 failing ... No test-level failure in this job ... Read the log."
- Also: `fx-tests test dom/tests/browser/browser_noopener.js` reports 0 failures on Linux (3,239 of 3,239 passed), because the killed runs never report a status, so the test looks healthy where it hangs about 2.6% of the time.
- Workaround: downloaded the 19 `live_backing.log` files and grepped for the last `TEST-START` before the timeout line.
- What would have helped: an "unfinished test" row in `task`, and in `test` counting runs whose job was killed while that test was running.

## Question: which jobs did the sheriffs annotate on this bug before the index window?

- Command: `fx-tests intermittent --bug 1851682 --since 21 --limit 0`
- Got: only the window (2026-09-02 on). Whether the noopener hangs predate a 2026-09-05 landing needed older data.
- Workaround: `curl "https://treeherder.mozilla.org/api/failuresbybug/?startday=2026-07-15&endday=2026-09-22&tree=trunk&bug=1851682"`.
