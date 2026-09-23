## `fx-tests try --task-ids` omits the failure message for a non-perma-fail row

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0`
- Expected: the `test_BHRShutdownAnnotation.js` block to name how it failed (`Test timed out`), as the PERMA-FAILS blocks do.
- Got: the block lists the task twice and the profile URLs, but no message; the "2/3" column in the summary table has no header in view to say it is failures/runs.
- Workaround: `fx-tests task SgvtLGZKSZS0SCWu7Q7IoQ --profiles` shows `TIMEOUT — 2 failing executions of 2 / Test timed out`.
