# profiler-cli feedback (browser_dbg-breakpoints-columns.js)

## Question: what was the machine's average / max CPU use over a time range of a resource-usage profile?

- Command: `profiler-cli zoom push 335,715 --session <s>` then `profiler-cli thread markers --search "name:CPU Use" --session <s>`
- Expected: a summary of the `CPU Use` markers' `cpuPercent` field over the zoom (mean, max), since that is the question a resource-usage profile is read for ("was the machine saturated when the timeout hit?").
- Got: duration stats of the markers only (min/avg/max interval length); `profile info` says "CPU activity over time: No significant activity" because the mach thread has no samples.
- Workaround: `--list --limit 0 --json` and a Python script averaging `fields[].value` for `cpuPercent` (3,804 markers).
