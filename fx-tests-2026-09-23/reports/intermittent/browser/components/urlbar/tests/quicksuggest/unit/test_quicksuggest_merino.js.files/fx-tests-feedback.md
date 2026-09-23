## Question: "what are the failures of the bug I was given?" (test-verify timeouts)
- Command: `fx-tests intermittent --bug 2003715`
- Expected: the jobs sheriffs starred on the bug.
- Got: exit 2, "no sheriff annotations ... between 2026-09-16 and 2026-09-22" (trunk, 7 days). Only `--since 30 --tree all` found the two annotated jobs. Meanwhile `fx-tests test <path>` (xpcshell data) never shows test-verify runs at all, so the bug's own failure mode (TV chaos-mode timeout) is invisible in `test`, `--history` and `--task-ids`, which instead list unrelated job-wide crash signatures.
- Workaround: widen the window by hand. Saying in `fx-tests test` that test-verify jobs are not counted, and defaulting `intermittent --bug` to a wider window when the default one is empty, would have saved several commands.

## Question: "how long did this test take in each passing execution of a job?"
- Command: `fx-tests task R250EIiVTBmQIB-vFKBCcQ --json`
- Expected: per-execution durations (to compare chaos-mode vs normal runs of a passing test-verify job).
- Got: `{'path', 'statuses', 'executionCount'}` only.
- Workaround: loaded the job's resource-usage profile and read its `test` markers. Durations in `task --json` would avoid a 0.6 GB profile load.
