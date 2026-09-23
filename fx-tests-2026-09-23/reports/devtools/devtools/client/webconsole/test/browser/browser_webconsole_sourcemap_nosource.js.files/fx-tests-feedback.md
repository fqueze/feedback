## `try --project autoland` calls a push's own failures "Pre-existing"

- Command: `fx-tests try 82333bc598c616e1d6ca3a1aba29d8583e6d538e --project autoland --task-ids --profiles --limit 0`
- Expected: failures that exist on central only because of this same push (every central failure of
  the test is on `82333bc598c6`, and it was backed out 32 minutes later) should be flagged as this
  push's, not as pre-existing.
- Got: `Pre-existing: central already fails the same way on the 3 configs it failed every run on
  (6 times in 21 days) — probably not yours.` Those 6 central failures are this push's own jobs.
- Workaround: `fx-tests test <path> --task-ids` showed every failure was on that one revision.

## Question: which task IDs failed for one test on one config of a push

- Command: `fx-tests try 82333bc598c6 --project autoland --task-ids --profiles --limit 0`
- The task list per row stays cut at 5 (`… 29 more tasks`) even with `--limit 0`, and `--config` is
  refused for `try`. To find the Windows task behind this test's failure, I had to go through
  `--json` (`permaFails[].taskIds`).
- It could show: the full task list with `--limit 0`, or grouped by config.

## `test` and `try` disagree on how often it failed on one revision

- `fx-tests test <path>` shows 6 failures in 3 jobs, all on `82333bc598c6`.
- `fx-tests try 82333bc598c6 --project autoland --test <path> --all-jobs` shows 17 failing job
  runs on that same revision (tsan 6, a11y-checks 7, windows opt 4).
- Nothing says why the central index holds only 3 of the 17 jobs.
