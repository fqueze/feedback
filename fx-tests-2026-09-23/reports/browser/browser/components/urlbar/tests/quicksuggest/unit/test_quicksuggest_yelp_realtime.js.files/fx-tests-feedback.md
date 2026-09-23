## Question: do the failing tasks cluster on a few workers?

- Command: `fx-tests test <path> --task-ids --limit 0`, then `curl .../api/queue/v1/task/<id>/status` per task for `runs[0].workerId`, then `sort | uniq -c`.
- Expected: the worker id next to each task id in `--task-ids` (or a `--by-worker` grouping), and the pool size, since "29 failures on 10 of 77 workers, 23 of them on 5" is what makes this an environment issue.
- Got: task ids and configs only.
- Workaround: 29 curl calls to the Taskcluster queue.

## `failures --message` and `crashes` disagree on "child process hang at shutdown"

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the rows behind the 19,349 crashes that `fx-tests crashes --harness xpcshell` lists under that signature, and that `fx-tests test <path>` shows under Issues.
- Got: "No failure matched. Searched 4,982 tests in xpcshell-issues.json".
- Workaround: `fx-tests crashes` for the count; no way found to list the tests behind the signature.
