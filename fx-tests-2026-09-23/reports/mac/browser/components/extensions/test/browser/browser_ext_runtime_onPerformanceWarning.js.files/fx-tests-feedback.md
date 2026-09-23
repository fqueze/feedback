## `test` Issues counts a TEST-KNOWN-FAIL as a run's first failure

- Question: what did this test fail with in each failing run?
- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_runtime_onPerformanceWarning.js`
- Expected: the vsync failure listed for all 37 runs, with the harmless todo lines left out.
- Got: `2. 7x FAIL handleEvent() was unable to perform a11y checks on hidden node: … notification-button …`, shown as a separate failure mode. In the per-test profile of one of those runs (UdI66YGZS-2xp2OUkn_7Rw) that line is a `TEST-KNOWN-FAIL` (an `a11yWarn` todo). The run's real failures are the two vsync `TEST-UNEXPECTED-FAIL`s that follow it. `fx-tests task <id> --messages` lists all three lines with no status, so they cannot be told apart there either.
- Workaround: loaded the per-test profile to read each marker's status.

## No revision or push date for a failing job, so a run on an old revision looks like a recurrence

- Question: were the failing jobs on revisions before or after the fix (fdf2baf6d9a5, 2026-09-04)?
- Command: `fx-tests test <path> --task-ids --limit 0` (and `--json`, where `taskIds[]` has `day` but no revision)
- Expected: each task's repo, revision, and push date (or at least the revision), so that a backfill on an old revision stands out.
- Got: only the day the job ran. `--history` shows 3 failures on 2026-09-14, which looks like the problem came back after the fix. In fact those jobs ran on revisions pushed on 2026-08-15. `fx-tests task <id>` prints the revision, but only one job at a time, and it reads the job's profile.
- Workaround: for each task, `curl …/api/queue/v1/task/<id>` to read `payload.env.GECKO_HEAD_REV`, then `hg json-pushes?changeset=<rev>` for the push date.
