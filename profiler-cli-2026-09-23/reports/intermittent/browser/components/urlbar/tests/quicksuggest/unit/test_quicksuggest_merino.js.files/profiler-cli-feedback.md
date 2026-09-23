## Question: "was the machine saturated while this test ran?" (resource-usage profile)
- Command: `profiler-cli counter list --session <s>` after `zoom push 791,913` on a `profile_resource-usage.json`.
- Expected: a CPU counter (or a summary) I could read over the zoomed range.
- Got: "No counters in this profile." Machine CPU is only in 11,000+ `CPU Use` markers, one per 100 ms, whose `cpuPercent` is a payload field. `thread markers --search "name:CPU Use"` gives counts and durations, never the mean/max of the field.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` piped to python to average `data.cpuPercent`. A mean/max/p90 of numeric payload fields in the aggregated `thread markers` view, or exposing these as a counter, would answer it.

## Question: "how long did each forceSync take?" (time between two log markers after t=60s)
- Command: `profiler-cli thread markers --search "forceSync Syncing Rust backend,forceSync Done syncing" --list --limit 0 --session <s>`
- Expected: timestamps precise enough to subtract.
- Got: past one minute, `--list` prints `t=1m13s`, i.e. 1-second resolution, so 0.4 s and 1.4 s intervals are unreadable.
- Workaround: `--json` and subtract `start` values in python. Printing e.g. `t=73.609s` (or `1m13.609s`) would be enough.

## Question: "how are this interval marker's durations distributed, and how did that shift between two profiles?" (review)
- Command: `profiler-cli thread markers --search DispatchTransaction --session <s>`
- Expected: median and a few percentiles next to min/avg/max, to compare two profiles' distributions (a fixed offset and a proportional scale-up look different).
- Got: `interval: min=73.981ms, avg=129.54ms, max=208.41ms` only.
- Workaround: `--list --limit 0 --json` and python for the median, p10, p25, p75 and p90. p50/p90 in the aggregate line would answer it. The same goes for `thread network`'s phases: it prints totals, and medians per phase would compare runs of different lengths.
