## Question: what was the machine's CPU use while one test ran?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` after `zoom push m-1` (the `test` marker) on a resource-usage profile.
- Expected: the CPU percent per row, or a summary (time-weighted mean, min/max) for the zoomed range.
- Got: 231 rows with only name, start and duration; the CPU percent is only in `marker info` per marker. `counter list` says "No counters in this profile", and `profile info` says "No significant activity".
- Workaround: `--json`, then a Python script computing the time-weighted mean of `data.cpuPercent` per 20 s bucket.
- What could have shown it: `profile info` / `counter info` treating the resource-usage `CPU Use` markers as a CPU track, or `--list` printing the marker's main field (CPU Percent) on each row.

## `thread samples-top-down` has no depth limit

- Command: `profiler-cli thread samples-top-down --root-at f-396 --max-depth 12`
- Expected: a depth-limited tree. Got: `error: unknown option '--max-depth'`.
- Workaround: `--root-at` a deeper function and `head`.
