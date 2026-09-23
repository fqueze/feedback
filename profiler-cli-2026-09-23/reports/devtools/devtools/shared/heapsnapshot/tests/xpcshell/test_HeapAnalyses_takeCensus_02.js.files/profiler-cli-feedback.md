## Status counts and start times of `test` markers (test_HeapAnalyses_takeCensus_02.js)

Question: "in this job, how many tests ended with each status, and when did the TIMEOUT ones start?"
Command: `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0 --json`, then a Python script grouping on the label prefix (`TIMEOUT — `, `PASS — `) and bucketing `start` by second.
Expected: `--group-by field:status` (or similar) on the `test` markers, with start-time min/max per group.
Got: the Text markers only carry the status inside `label`/`text`, so `--group-by` has no field to use.
Could have shown: a per-status count with first/last start time, the way `--auto-group` summarises names.

## Machine CPU over time in a resource-usage profile

Question: "was the machine busy or idle while the test's 45 s timer ran?"
Command: `profiler-cli profile info` says "No significant activity" and `counter list` says "No counters", yet the `CPU Use` markers show 100% at 46–51 s and 0% from 56 s on. Had to script over `thread markers --search "name:CPU Use" --list --json` to read `cpuPercent`.
Expected: `profile info`'s "CPU activity over time" to use the `CPU Use` markers of a resource-usage profile, or those markers to be exposed as a counter.
