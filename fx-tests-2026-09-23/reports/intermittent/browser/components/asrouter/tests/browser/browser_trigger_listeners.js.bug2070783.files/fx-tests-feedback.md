## `fx-tests task` does not list the hang profile a timed-out job uploaded

- Command: `fx-tests task TODFnvj6SI-19vuIMkVZqw --profiles`
- Expected: the profile the harness captured after the timeout (it logs "profiler Attempting to start the profiler to help with diagnosing the hang", SIGUSR1/SIGUSR2), which is uploaded as `public/test_info/profile_0_12371.json` (named after the main process pid).
- Got: "No failing test named a per-test profile in this job." and only the resource-usage profile.
- Workaround: listed the task's artifacts with `curl .../runs/0/artifacts` and found `profile_0_<pid>.json`. For an "application timed out ... with no output" hang, that profile is the only one with Firefox samples of the hang: it showed the main thread stuck in a synchronous loop.
