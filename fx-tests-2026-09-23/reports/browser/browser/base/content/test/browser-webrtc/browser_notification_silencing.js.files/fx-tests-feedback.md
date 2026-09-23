## `task --profiles` does not list the hang profile the harness captures before killing

- Command: `fx-tests task frtIdtGYQ8CXEFtb3aIqdg --profiles` (also Kx8bDTXkRV2t8sNsI4l_tQ, Cr2mTWMyRbKasNkZWAhIKw)
- Expected: for a Linux/mac "application timed out after 370.0 seconds with no output", the profile the harness records by sending SIGUSR1/SIGUSR2 to the hung browser ("Attempting to start the profiler to help with diagnosing the hang.") listed next to the failure, e.g. `public/test_info/profile_0_1695.json`.
- Got: "No failing test named a per-test profile in this job." The only way to find it was an `artifact` marker in the resource-usage profile, then listing the task's artifacts through the Taskcluster API.
- Workaround: `curl .../task/<id>/runs/0/artifacts?limit=1000` and look for `profile_0_<pid>.json`.
- Question it could have answered: "is there a profile of the hung browser for this timeout?" This is the single most useful profile for an app-level hang, and the only one there is.
