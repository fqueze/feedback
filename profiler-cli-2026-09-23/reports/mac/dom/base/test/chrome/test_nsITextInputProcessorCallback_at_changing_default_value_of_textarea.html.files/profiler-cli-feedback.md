## Summary of a marker field over a range needs a script

- Question: what was the whole-machine CPU use (mean, median, 90th percentile) over t=60–320s of a resource-usage profile, while the test waited?
- Command: `profiler-cli zoom push 60,320` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json`, summarised with Python over `data.cpuPercent` (2,603 markers).
- What the default output could have shown: `thread markers --search "CPU Use"` without `--list` aggregates durations only; a min/mean/median/p90/max of numeric payload fields (`cpuPercent`) would answer "was the machine saturated" directly.

## Default session directory not writable under the sandbox

- Command: `profiler-cli load <url> --session tipcb-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then `Unknown session`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call. The error message suggests it, which helped.
