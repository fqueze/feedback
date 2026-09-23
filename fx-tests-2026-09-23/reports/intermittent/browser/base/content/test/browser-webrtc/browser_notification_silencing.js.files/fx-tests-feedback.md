## `fx-tests task --profiles` misses the harness's hang profile

- Command: `fx-tests task Cr2mTWMyRbKasNkZWAhIKw.0 --profiles` (also Kx8bDTXkRV2t8sNsI4l_tQ.0, frtIdtGYQ8CXEFtb3aIqdg.0, ZGVBgvt9Q5ODb5iqfSjgtg.0, as5xmbDfTnye-rmQb-vy4w.0)
- Expected: the profile the harness captured of the hung browser listed next to the failure ("application timed out after 370.0 seconds with no output"), since it is the only per-process profile of the failure.
- Got: "No failing test named a per-test profile in this job." The task has `public/test_info/profile_0_1560.json` — the harness sends SIGUSR1/SIGUSR2 on Linux before killing a hung browser ("Attempting to start the profiler to help with diagnosing the hang.") and uploads `profile_0_<pid>.json`. It holds the whole session with the main thread's hung stack.
- Workaround: listed the task's artifacts with the Taskcluster queue API and grepped for `profile`.
- Cost: nearly concluded "no profile of the hang exists" from the resource-usage profile alone.
