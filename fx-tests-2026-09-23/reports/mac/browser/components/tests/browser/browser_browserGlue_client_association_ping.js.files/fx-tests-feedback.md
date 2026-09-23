## `task --profiles` does not list the hang profiles the harness captures on macOS

- Command: `fx-tests task eQt7Hs7TSpWl5VNXPAPeEw.0 --profiles` (also `NMbnCN1zQtmQXT0c9L3UoA.0`)
- Expected: for a test that ended in `application timed out after 370.0 seconds with no output` on macOS, the profile the harness captured from the hung browser (it sends SIGUSR1/SIGUSR2 and uploads `public/test_info/profile_0_<pid>.json`, one per hang) listed under that test.
- Got: the test listed with no profile at all, and for NMbnCN1zQtmQXT0c9L3UoA.0 "No failing test named a per-test profile in this job", which reads as "there is nothing to load". The two `profile_0_3432.json` / `profile_0_4232.json` artifacts were the only profiles showing the deadlocked main thread.
- Workaround: listed the task's artifacts from the Taskcluster queue API and matched the pid against the log.

## A harness-killed hang counts as N "failing executions"

- Command: `fx-tests task fY0wqB2MSZOmtuByGSgUVQ.0 --profiles`
- Expected: `TIMEOUT — 1 failing execution of 1` (the test ran once, then the harness killed the hung browser).
- Got: `CRASH, TIMEOUT — 9 failing executions of 9`, with `[Unknown]` as a crash signature. The 8 CRASH rows are the minidumps the harness itself wrote for each process it force-terminated, not crashes. This made it look like a crash plus repeated reruns.
- Workaround: read the job's resource-usage profile, where the "Writing a dump ... for [pid]" markers show the harness wrote the dumps.
