## Question: "how busy was the machine during this test?" (resource-usage profile)

- Command: `profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0` on a job's `profile_resource-usage.json`.
- Expected: some summary of machine CPU over the current zoom (average / timeline of `cpuPercent`), or the CPU tracks exposed as a counter so `counter info` gives the "over time" view.
- Got: `counter list` says "No counters in this profile"; the CPU values are only in per-marker payloads (~20,000 `CPU Use` markers), one row each, with no aggregation of the `cpuPercent` field.
- Workaround: `--list --limit 0 --json` piped into Python to bucket `data.cpuPercent` into 5 s duration-weighted averages.
- Could have shown: `thread markers --search "name:CPU Use" --stats-field cpuPercent` (min/avg/max per bucket), or the CPU markers turned into a counter track.
