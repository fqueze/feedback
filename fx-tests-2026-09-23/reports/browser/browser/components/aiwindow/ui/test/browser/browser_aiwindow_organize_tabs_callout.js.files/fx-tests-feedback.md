## Question: which push was the last to fail, relative to a fix landing?

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aiwindow_organize_tabs_callout.js --task-ids --limit 0 --day 2026-09-04`
- Expected: each failing task's repo and revision (or push time), so "the last failing push predates the fix push" can be read directly for a `no longer fails` report.
- Got: task ID, job name, day, message only. `--json` has no revision either.
- Workaround: looped over the 56 task IDs with `curl .../api/queue/v1/task/<id>` to read `GECKO_HEAD_REV`, then Treeherder's push API for push ids. Showing the repo and revision next to each task ID (or a "last failing revision" line with `--history`) would answer it.

## `--since` takes a day count, not a date

- Command: `fx-tests test <path> --task-ids --limit 0 --since 2026-09-04`
- Expected: failures from that date on. Got: `--since expects a non-negative integer`. Workaround: `--day`. Accepting an ISO date too would help, since `--history` prints dates.
