# fx-tests feedback — browser_autocomplete_secondary_actions.js

## Question: did the last failures run before or after the landing that stopped it?

- Command: `fx-tests test <path> --task-ids --limit 0 --day 2026-09-17`
- Expected: each task ID with its revision (or push time), so the failures can be placed before or after a landing on the same day.
- Got: task IDs and job names only. Placing them needed one `curl` per task to the Taskcluster queue (`payload.env.GECKO_HEAD_REV`, `created`) plus `json-pushes` for the landing's push.
- Workaround: loop over `curl https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<id>` with a python one-liner.
- What would have answered it: a revision (and push date) column in `--task-ids`, or a `--since-rev <rev>` filter.

## Question: when did one failure mode stop?

- Command: `fx-tests test <path> --history --issue 2`
- Expected: the per-day history of issue 2 alone (a Linux a11y-checks mode, 94 of 751 failures, invisible in the combined history).
- Got: `--issue selects which failure the printed task IDs belong to, so it needs --task-ids`.
- Workaround: `--task-ids --issue 2 --limit 0` piped through awk to count task IDs per date header.
- What would have answered it: `--history` honouring `--issue`.
