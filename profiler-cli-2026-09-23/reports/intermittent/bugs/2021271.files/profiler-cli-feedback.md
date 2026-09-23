## Question: what was the machine's CPU use around each app launch of a job (resource-usage profile)

- Command: `profiler-cli thread markers --session 2021271-1 --search "name:CPU Use" --list --limit 0 --json` plus `--search "Application pid" --list --json`, then a Python script averaging the CPU Use markers' `cpuPercent` in a +-3 s window around each launch marker.
- Expected: a way to get a CPU summary for a time window without scripting, e.g. `counter info` over a zoom — but this profile has "No counters", CPU is only in 17,860 `CPU Use` interval markers, and `thread markers` over a zoom lists them rather than aggregating their payload.
- Got: 25 MB of JSON to post-process.
- What could answer it: an aggregate (min/avg/max of a numeric marker field) for markers in the current zoom, e.g. `thread markers --search "name:CPU Use" --stats cpuPercent`.

## Question (review, again): what was the machine's CPU use in a window around each of a job's launches

- Command: `profiler-cli thread markers --session review-2021271-1 --search "name:CPU Use" --list --limit 0 --json` (17,860 markers), then a Python average over +-3 s around each `Application pid` marker.
- Same question as the entry above, hit again in review: to check a report's "87% vs 55-61%" I had to script it, and the result depends on whether the average is weighted by duration (55.2-59.8%) or not (55.3-60.6%).
- What could answer it: an aggregate over a zoom, e.g. `thread markers --search "name:CPU Use" --stats cpuPercent`, stating its weighting.
