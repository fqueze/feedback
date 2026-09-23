## Question: "what ran on this worker just before the failing job" (bug 915212, address-in-use mode)

- Command: `fx-tests task fyVJhiPATBKHsGLAXuMFzA --profiles`, then `fx-tests intermittent --bug 915212 --json` (which does carry `machineName` per occurrence).
- Expected: for a harness-level failure suspected to be left over by a previous job, the previous and next jobs on the same worker (task id, run, job name, result, end time).
- Got: nothing in fx-tests answers it. The Taskcluster worker `recentTasks` only covers the last ~20 tasks (~12 h on this pool), so it did not reach a failure 2 days old.
- Workaround: Treeherder `api/project/<repo>/jobs/?machine_name=<serial>&count=2000` for 5 repos per device (46 devices, 230 requests, some truncated at 2000), then matching the failing (task, run) and taking its neighbours (`prev_jobs.py`). The time filters I tried (`start_timestamp__gte`) were ignored.
- What would have answered it: `fx-tests task <taskId> --neighbours` (the jobs before and after it on the same worker), or a `worker` column plus a "previous job on this worker" column in `intermittent --bug`.

## Question: "which of the bug's occurrences are this failure mode" (bug 915212 and bug 1976612)

- Same problem as the one already logged in `915212.files/fx-tests-feedback.md`: `lines` holds only the starred line, so telling the address-in-use mode from the empty-`HOST_IP` mode meant downloading every log. It also hid that 16 of bug 1976612's 35 jobs are this mode.
