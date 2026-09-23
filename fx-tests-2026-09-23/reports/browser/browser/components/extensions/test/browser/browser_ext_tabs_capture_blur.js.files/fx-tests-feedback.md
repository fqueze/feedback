## Question: which test in this job started the cascade?

- Command: `fx-tests task IU6IH5UUTAGa8XAW48I_1Q --profiles --limit 0 --messages`
- Expected: failing tests in execution order (or with their start time), so that the first failure of a
  cascade (here, 45 tests all failing "waiting for vsync to be disabled") is the first row.
- Got: an order that is not execution order: `browser_ext_incognito_views.js` is listed first,
  though `browser_ext_commands_execute_page_action.js` ran (and failed) 20 tests earlier in the same
  manifest. `--json` has no position or timestamp per failure either.
- Workaround: a script over `--json` for all 33 tasks, ranking failures by the manifest's own order
  (fetched separately). The output could show, per failure, its index in the job, and flag
  "first failure of a run of N consecutive failing tests in this manifest".

## Question: did these failures happen on revisions with or without a given fix?

- Command: `fx-tests test <path> --history`
- Expected: a way to tell a recurrence from a retrigger/backfill on an old push.
- Got: per-day counts by run date. The 3 failures on 2026-09-14 look like a recurrence 10 days after
  the fix, but all three ran autoland revisions from 2026-08-15.
- Workaround: `fx-tests task <id> --json` for each task's revision, lando hg2git, then
  `git merge-base --is-ancestor`. The history could show the pushdate of the revision next to the
  run date, or `fx-tests test --task-ids` could print each task's revision and push date.
