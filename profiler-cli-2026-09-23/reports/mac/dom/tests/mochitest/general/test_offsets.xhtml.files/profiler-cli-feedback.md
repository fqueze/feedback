# profiler-cli feedback (test_offsets.xhtml)

## Question: the exact time of a marker after the first minute of a profile

- Command: `profiler-cli marker info m-93 m-94 m-23 m-96 --session test_offsets.xhtml-1`
- Expected: a time I can order and subtract, e.g. `t=305.836s`.
- Got: `Time: 5m6s (instant)` for every marker in that second; `thread markers --list` also prints `t=5m6s`. The test's start, its `must wait for load`, `must wait for focus` and the neighbour's end are all "5m6s", and the timeout-to-finish gap (607.938s to 608.989s) is "10m8s" to "10m9s".
- Workaround: `marker info ... --json` and read `start` (ms) with a Python one-liner.
- What would have answered it: millisecond precision in the text output past 60 s (e.g. `5m5.836s` or `305.836s`), as it already does under a minute.

## Question: how busy was the whole machine during a range (resource-usage profile)

- Command: `profiler-cli zoom push 330,625` then `profiler-cli thread markers --search "name:CPU Use"` on `profile_resource-usage.json`.
- Expected: the machine's CPU use over the range (median/mean/max of `cpuPercent`).
- Got: 2,945 `CPU Use` markers with duration stats only; `profile info` says "No significant activity" and `counter list` has nothing, since the CPU is in marker payloads.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python script over `data.cpuPercent`.
- What would have answered it: a summary of numeric payload fields for a marker group (e.g. `--group-by name` printing min/median/max of `cpuPercent`), or exposing the resource-usage CPU as a counter.
