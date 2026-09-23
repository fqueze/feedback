## Question: did this test fail on any push after a given landing?

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_history_redirect.js --task-ids --limit 0` (and `--json`)
- Expected: each failing task with its revision and push date, so "did it fail after the fix landed on 2026-09-04 15:45 UTC" is answered from one command.
- Got: task IDs grouped by the day the task ran, no revision. `--history` showed 3 failures on 2026-09-14, which read as a recurrence after the fix; they were retriggers run on 2026-09-14 against 2026-08-15 pushes (a78ff5819100, da79ac063c12, 54cd4c62c282).
- Workaround: `fx-tests task <id>` for each task to get the revision, then hg `json-pushes?changeset=<rev>` for its push date (a script over 15 tasks).
- What would have shown it: the revision (and push date or push id) on each `--task-ids` row, and in `--history` a note when a day's failures are on pushes much older than that day.
