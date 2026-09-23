## Question: which push stopped this failure (push time/revision of each failing task)

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_tab_move.js --task-ids --limit 0 --since 14 --issue 2`
- Expected: each failing task with its revision and push time, so the last failing push before a step change is visible, and the landing that ended it can be bracketed.
- Got: task IDs grouped by day only. Revision needs one `fx-tests task <id>` call per task (62 calls), and push time needs `hg json-pushes` per revision; the day buckets also mix autoland and central pushes of different ages.
- Workaround: loop over `fx-tests task`, then `curl https://hg.mozilla.org/integration/autoland/json-pushes?changeset=<rev>` and `git merge-base --is-ancestor <suspect> <git_changeset>` per push. What would have answered it: a `--task-ids` column with revision + push time, and ideally the last failing / first passing push per config after a step change in `--history`.

## Minor: `--since` takes days, not a date

- Command: `fx-tests test <path> --task-ids --limit 0 --since 2026-09-09`
- Got: `--since expects a non-negative integer`. Accepting a date (as `--day` does) would be natural after reading `--history`.
