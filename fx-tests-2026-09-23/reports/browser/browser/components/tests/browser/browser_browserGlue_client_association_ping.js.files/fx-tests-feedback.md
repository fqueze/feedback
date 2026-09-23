## `task --profiles` misses the hang profiles of "application timed out" failures

- Command: `fx-tests task eQt7Hs7TSpWl5VNXPAPeEw --profiles` (also `fx-tests test <path> --profiles`)
- Expected: for a test failing with "application timed out after 370.0 seconds with no output" on macOS/Linux, the profile the harness captures of the hung browser (log: "profiler Sending SIGUSR1 to pid 3432 start the profiler" ... "Symbolicating profile_0_3432.json"), uploaded as `public/test_info/profile_0_<pid>.json`. It holds the whole browser session and is the only per-test profile such a failure has.
- Got: "No failing test named a per-test profile in this job." for the hung test; only the resource-usage profile listed.
- Workaround: list the task's artifacts from the Taskcluster queue API and match `profile_0_<pid>` to the PID in the log's "Sending SIGUSR1 to pid" line.

## `task` / `crash` do not map a hang's dumps to processes

- Command: `fx-tests task fY0wqB2MSZOmtuByGSgUVQ --messages`, then `fx-tests crash fY0wqB2MSZOmtuByGSgUVQ <id>`
- Question: "which of the 9 dumps of this hang is the parent process". The job shows "CRASH, TIMEOUT — 9 failing executions of 9" (one per killed process, not 9 executions), and `crash` without `--thread 0` says "records no crashing thread".
- Workaround: read the log's "mozcrash Writing a dump to ...<id>.dmp for [<pid>]" lines and the "Killing process" of the parent pid, then `fx-tests crash <task> <id> --thread 0`.
- Could have shown: for kill-dumps of a timeout, the process type/pid per dump and the main thread by default.
