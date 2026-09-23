## Question: "how busy was the machine over this range"
- Command: `profiler-cli zoom push 208,243 --session <s>` then `profiler-cli thread markers --search "name:CPU Use" --session <s>`
- Expected: the mean/max of `cpuPercent` for the CPU Use markers in the zoomed range, the job's whole-machine CPU (this resource-usage profile has no counters: `counter list` says "No counters in this profile").
- Got: only the count and duration stats of the markers.
- Workaround: `--list --limit 0 --json` piped into a Python script averaging `fields[cpuPercent]`.
- Could have shown: numeric payload fields summarized (mean/min/max) in the aggregated `thread markers` view.
