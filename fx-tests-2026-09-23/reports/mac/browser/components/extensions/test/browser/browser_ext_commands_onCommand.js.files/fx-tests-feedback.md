# fx-tests feedback — browser_ext_commands_onCommand.js

## Question: is the same earlier test the first failure in every job where mine fails? (the leaker behind a cascade)

- Command: `fx-tests task <taskId> --profiles`, for each of the 44 failing jobs of
  `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_onCommand.js --task-ids --limit 0`.
- Expected: per job, the tests that failed in the same browser session in execution order, or at
  least which one failed first; across jobs, "in 44 of 44 jobs the first failure is
  browser_ext_commands_execute_page_action.js".
- Got: failures listed alphabetically, no timestamps in the text or in `--json`, and one job at a
  time. The job where 49 tests fail in a cascade reads the same as 49 independent failures.
- Workaround: a shell loop over `fx-tests task <id> --json` for all 44 tasks, a Python filter for
  the suspect's messages, and the resource-usage profile in profiler-cli for the order in one job.
- What would have answered it: an execution-order column (or start time) in `task`, and in
  `test --task-ids` a "first failure in the same browser session" column per job.

## Question: did any failure happen on a revision that includes the fix?

- Command: `fx-tests test <path> --history`, then `--task-ids --limit 0`.
- Expected: something that distinguishes the push date of the revision from the date the job ran.
- Got: 3 failures dated 2026-09-14, which read as a recurrence after the fix landed on
  2026-09-04. All three ran on autoland revisions pushed on 2026-08-15 (a78ff5819100,
  da79ac063c12, 54cd4c62c282), retriggered or backfilled weeks later.
- Workaround: `curl https://hg.mozilla.org/integration/autoland/json-rev/<rev>` for every
  revision to get its push date.
- What would have answered it: the push date next to each task id, or a `--history` keyed by
  push date, or a flag on runs whose revision is older than the window.

## `--bugs` prints nothing when there is no bug

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_onCommand.js --bugs`
- Expected: a "Bugs: none" line.
- Got: the usual output, with no Bugs section at all, so "no bug" and "flag ignored" look the same.
  `--bugs` on the neighbouring browser_ext_commands_execute_page_action.js printed nothing either,
  while Bugzilla quicksearch finds bug 2072654 for it, filed 2026-09-16; it may have no sheriff
  annotation yet, which would explain its absence, but the output does not say.
- Workaround: Bugzilla REST quicksearch.

## Failure modes split by a per-run id

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_execute_page_action.js`
- Expected: one issue, `uncaught rejection: PageActions: No anchor node for _<id>_`, ×46.
- Got: 49 one-off issues, one per extension UUID, `… 40 more`, burying the mode's count.
- Workaround: counting by hand across the `task --json` messages.
