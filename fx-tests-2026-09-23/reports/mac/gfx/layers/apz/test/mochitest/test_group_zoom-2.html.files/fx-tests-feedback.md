## `fx-tests task --profiles` does not list the hang profile of an application timeout

- Command: `fx-tests task cXC_WwPnQY6-TTry6JPt8w.0 --profiles` (also JnF0MnLXTJGgB6-CG9mPYg.0, FpyEFt6NTaeR0OKJDTpbbg.1)
- Expected: for `test_group_zoom-2.html | application timed out after 370.0 seconds with no output`, the URL of the profile the harness captured of the hung browser.
- Got: no profile for the timed-out test ("No failing test named a per-test profile in this job"), only `profile_resource-usage.json`. Yet the resource-usage profile has `WARNING Attempting to start the profiler to help with diagnosing the hang.`, and the task's artifact list has `public/test_info/profile_0_<pid>.json` (`profile_0_4821.json`, `profile_0_41467.json`, `profile_0_24422.json`) next to the minidump.
- Workaround: listed the artifacts with `curl .../api/queue/v1/task/<id>/runs/<n>/artifacts` and loaded `profile_0_<pid>.json` directly.
- Also: the minidump ID is in the CRASH marker's payload in the resource-usage profile (`Minidump: 59de46bf-...`), but `fx-tests test --task-ids` said no minidump was uploaded for the timeout's crashes, and printed none for these tasks.

## `fx-tests task` counts the dump of a killed hung browser as a second execution

- Command: `fx-tests task JnF0MnLXTJGgB6-CG9mPYg.0 --profiles`
- Expected: `TIMEOUT — 1 failing execution of 1`, with the crash signature attached to it.
- Got: `CRASH, TIMEOUT — 2 failing executions of 2`. The resource-usage profile has one `test` marker (TIMEOUT), and a CRASH marker 40 s later. That marker is the minidump the harness takes when it force-terminates the hung browser (`WARNING Force-terminating active process(es).`), not a second run.
- Cost: I looked for a harness rerun that did not exist.
