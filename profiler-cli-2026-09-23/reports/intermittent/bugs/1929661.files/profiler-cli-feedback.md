## Question: what was the machine's CPU use during this time range?

- Command: `profiler-cli thread markers --session 1929661-1 --search "name:CPU Use" --list --limit 0 --json`, then a script bucketing `cpuPercent` / `system_pct` / `user_pct` per minute.
- Context: resource-usage profile of a job that hung silently for 1000 s; the question was whether the hang was busy (spinning) or idle. The profile has no counters (`counter list` is empty) and `profile info` says "No significant activity"; CPU use is only in 10,936 `CPU Use` markers.
- What the output could have shown: `thread markers --search "name:CPU Use"` in aggregate mode (or under a `zoom push`) giving min/avg/max of the numeric payload fields (CPU %, user %, system %, iowait %), or `profile info` summarizing the resource-usage CPU markers as its CPU activity over time.
