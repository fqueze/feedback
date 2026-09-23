## resource-usage profile: machine CPU is in markers, and `profile info` says there is none
- Command: `profiler-cli profile info` / `profiler-cli counter list` on `profile_resource-usage.json` (task Xxr5rCPkSwCY8T4P8nONtQ)
- Expected: some pointer to the machine's CPU use, which the brief says the resource-usage profile has.
- Got: "CPU activity over time: No significant activity." and "No counters in this profile." The data is there, but as `CPU Use` interval markers with `cpuPercent` in their payload.
- Workaround: `thread markers --search "name:CPU Use" --list --json`, then a script to sample `cpuPercent` over time. Question: "how busy was the machine between t1 and t2?" A per-bucket summary of `CPU Use` markers in `profile info`, or under a zoom, would answer it.

## Question: "how often per second did marker X fire, over time?" (review-browser_compatibility_event_selected-node-change.js)
- Command: `profiler-cli thread markers --search "waiting for paint" --list --limit 0 --json`, then a script bucketing `flatMarkers[].start` per second and counting by `name`.
- Expected: a rate over time for one marker name, the way `counter info` gives "over time" buckets.
- Got: only the flat list. The substring search also matched `RefreshObserver` and `RefreshDriverTick` markers (474 hits, 438 of them the named marker), which is how the report under review got 474.
- What the output could show: an "over time" bucket line per marker name in the aggregated `thread markers` view, or under a zoom.
