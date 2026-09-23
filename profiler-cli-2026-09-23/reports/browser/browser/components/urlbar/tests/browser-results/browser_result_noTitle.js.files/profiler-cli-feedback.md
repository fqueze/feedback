## Question: "what was the machine's CPU use in the seconds around the failure?" (resource-usage profile)

- Commands: `profiler-cli profile info --session browser-noTitle-res` on `W_6HL6CpRVeAG1cjWgN8mw/.../profile_resource-usage.json`, then `counter list`, then `thread markers --search "CPU" --list`.
- Expected: a CPU-over-time summary (profile info's "CPU activity over time", or a counter) for the zoomed range.
- Got: `profile info` says "CPU activity over time: No significant activity." and `counter list` says "No counters in this profile.", while the profile has one `CPU Use` marker per 100 ms carrying `CPU Percent` (30-64% around the failure). The `--list` output shows only name and duration, not the percentage, so each needs `marker info`.
- Workaround: `thread markers --search "name:CPU Use,name:FAIL,name:IO" --list --limit 0 --json` and a Python script printing `data.cpuPercent` / `write_bytes` per marker. The list could show the main payload field (CPU Percent, write_bytes) inline, or `profile info` could summarize `CPU Use` markers as it does CPU counters.
## `load` of a raw Taskcluster URL selects a content process's main thread (review)

- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/ImrjRqWGTDedEmrO1NNOdA/runs/0/artifacts/public/test_info/profile_browser_result_noTitle.js.json" --session browser-review-noTitle-3`, then `thread markers --search "Idle (0),places::" --list`.
- Expected: the parent process main thread (t-0) selected, as when loading the profiler.firefox.com link of the same profile with `thread=0`.
- Got: `Selected thread: t-40 (GeckoMain, Privileged Content)`; the marker query answered "No markers match", which read as "no such runnables" until I noticed the thread in the header.
- Workaround: `thread select t-0` after every load. Selecting the parent GeckoMain by default, or saying which thread was picked and why, would avoid the silent miss.
