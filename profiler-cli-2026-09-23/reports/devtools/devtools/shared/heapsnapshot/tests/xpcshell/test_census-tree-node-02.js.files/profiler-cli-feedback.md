## Question: "from which moment did every test in this job start failing?"

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json`, then a Python script bucketing test markers by start time and status.
- The resource-usage profile of a job where 1,149 tests timed out after a machine-level failure (task PyUxuOpdQj2b3T3XyQOKpg): the default output gives per-name counts, not how test outcomes change over time.
- What could have shown it: `thread markers --group-by field:status` combined with a time bucketing (e.g. `--bucket 5s`), or a per-status count in `zoom push` views.

## Question: "which test did this launch_application marker belong to?"

- Command: `profiler-cli marker info m-1207 --session <s>` (xpcshell Android resource-usage profile, task TaA1l_CkQV2GHd7iFyy8IA).
- The `launch_application: am startservice ... XpcshellTestRunnerService$i3 ...` INFO marker's message is cut in `marker info`, and the test name (`_TEST_NAME`) and log file (`out_file`) are at its very end; I needed `--json` and a regex to pair slot launches with tests.
- What could have shown it: `marker info` printing the full message (or a `--full` flag), or the marker carrying the test name like other TestStatus markers.

## (review) Question: "how long after each test's start did its own log line come?"

- Command: `profiler-cli thread markers --session <s> --search "will retry" --list --limit 0 --json` and `--search name:test ... --json`, then a Python script that joins each TIMEOUT `test` marker to its "<test> failed or timed out, will retry." INFO marker by test id and subtracts the start times (task PyUxuOpdQj2b3T3XyQOKpg).
- Why: all 1,149 "TIMEOUT"s had their "will retry" line within 0.13 s of the start. That showed the launch had failed at once, and that the 45 s TIMEOUT came later from an uncancelled timer. The default output cannot link a marker to the log lines of the same test when those lines carry no `test` field.
- What could have shown it: `thread markers --search X --within name:test` (or `--group-by field:test`) listing, for each interval marker, the matching instant markers inside it with their offset from its start.

## (review) `profile-link.py` refuses a session loaded from a profiler.firefox.com link

- Command: `profiler-cli load 'https://profiler.firefox.com/from-url/…/marker-table/?marker=3703&thread=0&v=17' --session s`, then `python3 profile-link.py --session s --marker m-4428`.
- Expected: a link, because the review brief says to check links by loading them.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself".
- Workaround: stop the session and reload the raw Taskcluster URL, which means a second 0.6 GB load. `profile-link.py` could take the artifact URL out of the `from-url/` path.
