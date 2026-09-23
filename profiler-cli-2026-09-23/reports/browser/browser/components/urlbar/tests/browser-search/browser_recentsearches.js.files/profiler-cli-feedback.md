## `thread markers --list` rounds times to the second past one minute

- Question: the order and ms timing of the test's steps, the formhistory transaction and the query's completion, all within one second at t=66 s.
- Command: `profiler-cli thread markers --category Test --search browser_recentsearches.js --list --limit 0 --session browser-browser_recentsearches.js-2`
- Expected: `t=66.495s`-style times.
- Got: `t=1m6s` on every one of ~50 rows.
- Workaround: `--json`, `flatMarkers[].start` in a script.

## `thread markers --list` shows no values for resource-usage `CPU Use` / `IO` markers

- Question: machine CPU % and bytes written during a 300 ms stall.
- Command: `profiler-cli thread markers --search "name:CPU Use,name:IO" --list --limit 0 --session browser-browser_recentsearches.js-3` after `zoom push 453.9,455.1`
- Expected: `cpuPercent`, `idle_pct`, `write_bytes` per row.
- Got: bare `CPU Use` / `IO` rows with durations only.
- Workaround: `--json`, `flatMarkers[].data` in a script.

## `profile info` on a resource-usage profile says "No significant activity" and `counter list` says "No counters"

- Command: `profiler-cli profile info --session browser-browser_recentsearches.js-3` (profile_resource-usage.json of eQrfQxM7Sz29_pGFmbIATA)
- Expected: a pointer to the 8,188 `CPU Use` markers that carry the machine's CPU.
- Got: "CPU activity over time: No significant activity." and "No counters in this profile.", which reads as "no CPU data".
- Workaround: `thread markers --top-names 30` to find them.
