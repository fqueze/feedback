## Question: "was the machine CPU-saturated over this range / before each of these markers?"
- Profile: the resource-usage profile (`profile_resource-usage.json`), where `counter list` says "No counters" and CPU exists only as about 20k `CPU Use` interval markers carrying `cpuPercent`.
- Command: `profiler-cli thread markers --search 'name:CPU Use' --list --limit 0 --json`, then a Python script to take the duration-weighted mean over windows, and over the 20 s before each "may be hanging" marker.
- What could have answered it: a summary of `CPU Use` (duration-weighted mean, min, max of cpuPercent) in `thread markers` output for the current zoom, or exposing these markers as a counter so `counter info` works on it after `zoom push`.
