## `zoom push` silently misparses the time format profiler-cli itself prints

- Command: `profiler-cli zoom push 15m50s,16m40s --session browser-browser_smartwindow_topsites.js-1` (times copied from `thread markers --list`, which prints `t=16m3s`)
- Expected: a zoom on 950s–1000s, or an error saying only seconds are accepted.
- Got: `Zoom depth: 1` and a view of 15.0s–16.0s (the `m`/`s` were dropped), so the next query answered for the wrong range without warning.
- Workaround: convert to seconds by hand (`zoom push 955,1000`).

## Question: "machine CPU and memory over a time range, bucketed" (resource-usage profile)

- Needed: whether the machine was idle or busy, and memory used, per second over the 30 s before an OOM crash, and per 30 s over a whole manifest.
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` (and `name:Memory`), then a Python script to bucket `cpuPercent` and `used`.
- What could have shown it: the resource-usage CPU Use / Memory markers are really time series; exposing them as counters (`counter info` with its "over time" buckets), or a `thread markers --search "CPU Use" --bucket 2s` summary of their numeric fields, would answer directly. `thread markers` without `--list` only gives the interval stats of the markers, not their payload values.
