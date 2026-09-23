## Machine CPU use over a time window (resource-usage profile)

Question: "what was the machine's CPU use, averaged per 5 s, from 8m15s to 8m45s?"

- Command: `profiler-cli thread markers --session <s> --search "CPU Use" --list --limit 0` inside a `zoom push`.
- Expected: some per-bucket summary of the `cpuPercent` field (or a counter track), since "was the machine saturated" is the first question for any timeout or hang.
- Got: one row per `CPU Use` marker (about 8 per second) with only its duration; `cpuPercent` shows only in `marker info`. The profile has no counter (`counter list`: "No counters in this profile"), and `thread info` says "No significant activity".
- Workaround: script calling `marker info --json` on all handles in batches and averaging `cpuPercent` weighted by duration (`cpu_avg.py` in this directory).
- What could answer it: `thread markers --search "CPU Use" --stats-field cpuPercent` (min/avg/max per bucket), or exposing the CPU markers as a counter.
