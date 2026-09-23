## Question: how busy was the machine over time, in the resource-usage profile?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0 --json`, then a Python script bucketing `cpuPercent` / `user_pct` / `system_pct` into 15 s and 30 s bins.
- Expected: something like `counter info`'s "over time" section for the machine's CPU. The resource-usage profile has no counters (`counter list` says "No counters in this profile"); CPU is carried by 20,667 `CPU Use` interval markers.
- Got: `profile info` says "CPU activity over time: No significant activity." on this profile, and the marker summary only gives durations, not the payload values.
- Could have shown: an "over time" table of a numeric marker field (e.g. `thread markers --search "name:CPU Use" --over-time cpuPercent`), or `profile info` using the `CPU Use` markers when the profile has no samples.

## Question: when do the markers matching a search occur (a burst or spread out)?

- Command: `profiler-cli thread markers --search "may be hanging at shutdown" --list --limit 0`, then `grep -o "t=..."` to get a time series.
- Expected: a histogram or time-bucket count of matching markers.
- Got: only a flat list (110 rows); had to extract times with grep.
- Could have shown: a `--histogram <bucket>` option on `thread markers`.

