## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/sessionstore/test/browser_reopen_all_windows.js --bugs`
- Expected: a "Bugs" section, or a line such as "No sheriff-annotated bug names this test".
- Got: output identical to the run without `--bugs`. Nothing says whether the lookup ran and found nothing, or failed without a message.
- Workaround: read the fixing bug number from `git log` on the test file.

## Question: "on which revision did each failure on day X run, before or after the fix landed?"

- Command: `fx-tests test <path> --task-ids --limit 0 --day 2026-09-03` (and `--json`).
- It gives task IDs and job names, but not each task's revision. So I had to run `fx-tests task <id>` for each task to learn whether the day's 21 failures predate the fix.
- What would have answered it: the revision (and push time) next to each task ID in the `--task-ids` listing.

## `--since` takes a number of days, not a date

- Command: `fx-tests test <path> --task-ids --limit 0 --since 2026-09-03`
- Got: `--since expects a non-negative integer`. The error was clear, but `--day` takes a date, and a date is the natural input for "since the step change". Accepting a date for `--since` would help.
