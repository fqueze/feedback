## Question: when in the job did this test run, and how long was the browser startup before it?

- Command: `fx-tests task BWi2ZqXnRTicMtt2hSDOvA --passed` (and `--json`)
- Expected: each test's start time in the job, and for standalone jobs the gap since the previous test ended (that gap is the browser startup). This is what showed that the failing run's startup took 11.1 s instead of the usual 2.6 s, and that the manifest was first in the job.
- Got: outcomes only. The rows have no start time or order (`path, statuses, executionCount, testProfiles, ...`).
- Workaround: loaded the job's resource-usage profile into profiler-cli, then ran a script over `thread markers --search name:test --list --json` to compute the gaps.
- What the output could show: a start-time column (and a "gap before" column in standalone jobs) in `fx-tests task --passed`.
