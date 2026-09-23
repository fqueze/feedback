## Question: "the shutdown-hang profile of this job"

- Command: `fx-tests task FlAFdUWsQ7qbgeQYGvKXLA --profiles`
- Expected: the job's `public/test_info/profile_shutdown_hang_928.json` listed next to the resource-usage profile (the log says `TEST-UNEXPECTED-FAIL | shutdown hang | profile uploaded in profile_shutdown_hang_928.json`).
- Got: only the resource-usage profile, and "No test-level failure in this job ... Read the log."
- Workaround: listed the task's artifacts with `curl .../api/queue/v1/task/<id>/runs/0/artifacts` and grepped for `profile`.

## Question: "which jobs hit this non-test crash (terminator shutdown hang) on reftest/crashtest"

- Command: `fx-tests errors --message "CreateRemoteThread"` (defaults to mochitest; `--harness` only accepts xpcshell|mochitest)
- Expected: a way to ask the same of reftest/crashtest jobs, where 8 of the 9 annotated failures of bug 1944623 are.
- Got: mochitest only.
- Workaround: downloaded each annotated job's live_backing.log and grepped.

## Question: "on which configurations did this message appear on that day"

- Command: `fx-tests errors --file process_watcher_win --config asan`
- Expected: the rows restricted to ASan jobs, to compare the `CreateRemoteThread: error 5` rate on ASan against debug.
- Got: `--config cannot be applied to mochitest-<date>-errors.json: the file records no job names`.
- Workaround: `--task-ids --json` for each message, then a script fetching each task's `metadata.name` from the Taskcluster queue (`jobnames.py`). It showed 11/11 ASan attempts failing with error 5 against 0/118 debug ones.
