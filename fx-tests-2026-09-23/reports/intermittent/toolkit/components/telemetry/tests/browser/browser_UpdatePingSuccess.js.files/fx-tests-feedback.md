## Hang profile captured on application timeout not listed

- Command: `fx-tests task YQwCOM0hTMCogZNuGfIcOg --profiles`
- Expected: the profile the harness captures on "application timed out" (SIGUSR1/SIGUSR2 to the
  main pid), i.e. `public/test_info/profile_0_5915.json`, listed under the timed-out test.
- Got: only the resource-usage profile; the timed-out test shows no profile at all.
- Workaround: listing the task's artifacts via the Taskcluster queue API found `profile_0_<pid>.json`.
