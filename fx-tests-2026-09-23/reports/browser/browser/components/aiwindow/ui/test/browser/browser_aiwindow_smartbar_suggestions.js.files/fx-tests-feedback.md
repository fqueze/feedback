## Question: which push is the first failing one, after a step change in --history?

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aiwindow_smartbar_suggestions.js --task-ids --limit 0 --issue 1` (and `--json`).
- Expected: after `--history` shows a step (0 failures until 2026-09-07, then daily), a way to get the earliest failing task's revision and time, so the regressing push can be named.
- Got: task IDs grouped by day only. `taskIds[]` in the JSON has `taskId, retryId, jobName, chunk, status, day`, with no revision, repo or timestamp, and the order within a day is not chronological.
- Workaround: `curl https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<id>` for each 09-07 task, reading `created` and `payload.env.GECKO_HEAD_REV`, then the Treeherder push API for the push's commits. Here that found a single-commit push (bug 2054460) as the first failure.
- Could show: revision and push time per task-id row, or a `--first-failure` line under `--history`.
