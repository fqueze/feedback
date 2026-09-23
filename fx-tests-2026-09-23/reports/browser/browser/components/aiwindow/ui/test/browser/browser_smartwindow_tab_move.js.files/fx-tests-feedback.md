## Which revisions the failures before and after a step change were on

- Question: the failure stopped on 2026-09-10; which landing stopped it? That needs the revision of each failing task around the step, to test ancestry against candidate landings.
- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_tab_move.js --task-ids --limit 0 --issue 2 --since 13` (and its `--json`)
- Expected: the revision (and push time) next to each task ID, or a `--history` split by revision/push rather than by day.
- Got: task ID, job name and day only; the JSON `taskIds[]` entries have no revision either. `fx-tests task <id>` prints the revision, but one call per task.
- Workaround: `curl` each task's definition from the Taskcluster queue for `payload.env.GECKO_HEAD_REV` and `created`, then Lando `hg2git` and `git merge-base --is-ancestor`.

## `--since` takes a day count, not a date

- Command: `fx-tests test <path> --task-ids --limit 0 --since 2026-09-10`
- Expected: failures since that date (the output everywhere prints dates).
- Got: `fx-tests: --since expects a non-negative integer, got "2026-09-10"`.
- Workaround: `--since 12`.
