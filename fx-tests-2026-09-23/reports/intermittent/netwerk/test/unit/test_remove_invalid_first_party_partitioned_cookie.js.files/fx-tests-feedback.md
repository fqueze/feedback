## Question: did the last failures run before or after a given landing?

- Command: `fx-tests test <path> --task-ids --limit 0 --day 2026-09-09`
- Expected: each task's tree and revision (or push time) next to its task ID, so the last failing push can be compared with the landing that stopped the failures.
- Got: task IDs and configs grouped by day only.
- Workaround: ran `fx-tests task <id>` once per task (17 calls) to read the revision off its second line, then asked the Treeherder push API for push times.
- What would answer it: a revision column (and push time) in the `--task-ids` list, or a `--history --by-push` view.

## `--since` takes a day count, not a date

- Command: `fx-tests test <path> --task-ids --limit 0 --since 2026-09-08`
- Expected: failures since that date (other tools, and `--day`, take dates).
- Got: `--since expects a non-negative integer, got "2026-09-08"`.
- Workaround: `--day 2026-09-09`. Accepting a date here, or saying "days" in the error, would save the round trip.
