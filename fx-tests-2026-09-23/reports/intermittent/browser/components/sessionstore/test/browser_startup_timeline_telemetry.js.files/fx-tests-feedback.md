## Question: which profile shows the hang of a test that timed out with "application timed out after N seconds with no output"?

- Command: `fx-tests task FUsj5tAeQhuo_yMJMTCgDA --profiles` (same for `cLeFiMiBQpydrlcb6pNR6w`).
- Expected: the timed-out test listed with the profile the harness takes of the hung browser (it sends SIGUSR1/SIGUSR2 to it and uploads `public/test_info/profile_0_<pid>.json`).
- Got: the test listed with no profile at all, and on some jobs "No failing test named a per-test profile in this job". That `profile_0_2771.json` was the one profile showing the cause: the main thread blocked in `GleanQuantity.testGetValue` for 6m35s.
- Workaround: listed the task's artifacts with curl on `/api/queue/v1/task/<id>/runs/0/artifacts` and looked for `profile_*.json`.

## "10 failing executions of 10" for a single timed-out run

- Command: `fx-tests task TlVBQEElQMeu8rl9OAcDoQ --profiles` (and `XO1e1npMRSi98-S2Eu6KIw`).
- Expected: one failing execution, a TIMEOUT.
- Got: `CRASH, TIMEOUT — 10 failing executions of 10` plus `[Unknown]`. The 9 extra "executions" are the CRASH lines the harness logs for the dumps it takes of each process when it kills the hung browser (the resource-usage profile has 9 `CRASH [Unknown]` markers after the TIMEOUT). These read like 10 reruns.

## `fx-tests crash` says a dump "is not there" when it is there but empty

- Command: `fx-tests crash TlVBQEElQMeu8rl9OAcDoQ 72505f1f-74cb-4d47-8b6d-24f36bade9ba`
- Expected: the parent's hang stack, or a message saying the dump is empty or truncated.
- Got: `no minidump ... the artifact is not there ... Taskcluster expires artifacts`. The artifact exists (HTTP 200) but is 61 bytes long, and the task is one day old. The message sent me looking for expiry.
