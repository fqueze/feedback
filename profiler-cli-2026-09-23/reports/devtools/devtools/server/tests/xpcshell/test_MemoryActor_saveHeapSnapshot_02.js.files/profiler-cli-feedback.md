## Was the machine saturated while this test ran? (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search 'name:CPU Use' --list --limit 0` after `zoom push 429,468`
- Expected: the machine's CPU % per row (or a summary over the zoomed range: mean/min/max CPU %), since that is the whole point of the CPU Use markers.
- Got: 304 rows of `CPU Use  t=7m9s  178ms` with no value; the percentage is only in `marker info` or `--json`.
- Workaround: `--json` piped to a Python script bucketing `data.cpuPercent` per second, weighted by duration.
- What the output could have shown: the `cpuPercent` field on each list row, or a one-line "CPU Use over range: mean 97%, min 93%" summary under the grouped view.

## When in the job did one warning happen? (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search 'hanging at shutdown; attempting' --list --limit 0`
- Expected: a way to see how 143 matching markers are distributed over the job's timeline (a per-minute histogram or sparkline, as `counter list` does).
- Got: a flat list of 143 rows only.
- Workaround: `--json` and a Python per-minute bucket count.
