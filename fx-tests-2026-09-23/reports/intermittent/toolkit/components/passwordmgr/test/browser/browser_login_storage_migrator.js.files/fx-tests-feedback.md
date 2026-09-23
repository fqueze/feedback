## Question: "did any failure happen on a revision that contains the fix?"

- Command: `fx-tests test toolkit/components/passwordmgr/test/browser/browser_login_storage_migrator.js --task-ids --limit 0 [--json]`
- Expected: the revision (and repo) of each failing task, or a way to restrict to tasks on/after a given revision, so I can tell whether failures continued after the fix (autoland 707cfe811edc) landed on the last day of the window.
- Got: task IDs grouped by day only; the JSON `taskIds[]` has no revision/repo/push field. `fx-tests task <id>` shows the revision, but one call per task.
- Workaround: fetched 357 Taskcluster task definitions (`/api/queue/v1/task/<id>` -> `payload.env.GECKO_HEAD_REV`/`GECKO_HEAD_REPOSITORY`) and then `json-pushes?changeset=` for each revision to compare push IDs with the fix's push.
- What would have answered it: a `rev`/`repo`/`pushId` column in `--task-ids` output, or a `--since-rev <rev>` filter on `fx-tests test`.
