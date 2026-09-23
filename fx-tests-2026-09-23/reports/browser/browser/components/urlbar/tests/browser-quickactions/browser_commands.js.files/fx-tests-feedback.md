## Question: which revision/push was the last to fail, to match a step change to a landing

- Command: `fx-tests test browser/components/urlbar/tests/browser-quickactions/browser_commands.js --task-ids --limit 0 --day 2026-09-08` (also tried `--json`)
- Expected: each failing task with its push time and revision (and tree), so the last failing push can be set against the fix's landing time.
- Got: only task ids and job names; the JSON `taskIds[]` has no revision or timestamp either. `--history` gives days only.
- Workaround: `fx-tests intermittent --bug 2049056 --tree all --since 21 --limit 0` and read the Occurrences table (push time, tree). That only works when there is a bug, and it only covers annotated jobs.
- Minor: `--since 2026-09-08` fails with "expects a non-negative integer"; accepting a date, or pointing to `--day`, would help.
