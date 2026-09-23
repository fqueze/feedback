## Question: "how busy was the machine during this window?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use"` (after `zoom push 150,171`), and with `--list`.
- Expected: some summary of the CPU Use markers' `cpuPercent` over the view (mean / min / max, or per-second buckets).
- Got: only marker counts and durations; `--list` prints one row per ~125 ms sample with no CPU value in the row, so reading 170 rows via `marker info` is the only way.
- Workaround: `--list --limit 0 --json`, then a Python script averaging `fields[cpuPercent]` per second / per minute.
- What would have answered it: numeric-field stats (mean/min/max) in the aggregated view for marker types with numeric payloads, or the `cpuPercent` value shown in the `--list` row label for CPU Use markers.
