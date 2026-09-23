## Question: were these failures before or after the fix landed?

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-backgroundtask-debugging.js --task-ids --limit 0 --since 10` (and the same with `--json`)
- Expected: each task ID with its push revision and push/creation time, so I can tell whether the last timeouts on 2026-09-11 came before the fix for bug 1767707 landed on autoland (502d0990429d, 11:31 UTC that day).
- Got: only the day (`2026-09-11`). The JSON has `taskId`, `jobName`, `day`, `status`, but no revision and no timestamp.
- Workaround: `curl https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<id>` for each task, reading `created` and `payload.env.GECKO_HEAD_REV`.
- What would have answered it: a revision column (and an hour next to the day) in the `--task-ids` list.
