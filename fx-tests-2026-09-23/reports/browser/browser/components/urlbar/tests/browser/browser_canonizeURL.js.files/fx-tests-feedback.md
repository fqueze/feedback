## Question: which push started, and which push stopped, a step-change failure?

- Command: `fx-tests test browser/components/urlbar/tests/browser/browser_canonizeURL.js --task-ids --limit 0` (and `--json`)
- Expected: each failing task's repo and revision (or push time), so the first and last failing pushes can be read off, next to `--history` which only has days.
- Got: task id, job name, day, message, occurrences; no revision. `--history` is per day only.
- Workaround: `fx-tests task <id>` per task to get its revision, then Treeherder's push API for timestamps, and git log for the landing. Showing the revision range (first/last failing push per repo) in `--history` or `--task-ids` for a step change would have answered it directly.
