## `task --profiles` misses the hang profile of an "application timed out" failure

- Command: `fx-tests task U2v7JPX_QoSHC1z1MgcUiA.0 --profiles` (also KhhbQESgR-uiAgd9Er2YEg.0, IzLvJuW_QSW76Nk0UzpI7Q.0)
- Expected: for a `TEST-UNEXPECTED-TIMEOUT | ... | application timed out after 370.0 seconds with no output`, the
  profile the harness captures of the hung browser (SIGUSR1/SIGUSR2, uploaded as
  `public/test_info/profile_0_<pid>.json`) listed under the failing test, and on Windows the `.dmp`
  minidumps with their IDs for `fx-tests crash`.
- Got: "No failing test named a per-test profile in this job." Only the resource-usage profile.
- Workaround: `curl .../api/queue/v1/task/<id>/runs/0/artifacts` and grep for `profile_0_` / `.dmp`.
