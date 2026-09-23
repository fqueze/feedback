## Machine CPU over a time range in a resource-usage profile

- Question: "was the machine saturated between t=22m17s and t=22m35s?" (xpcshell resource-usage profile, CPU only recorded as `CPU Use` interval markers, `counter list` says no counters).
- Command: `profiler-cli zoom push 1337,1355 --session S; profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0`
- Expected: a per-bucket CPU % summary (like `counter info`'s "over time" section) for `CPU Use` markers in the zoomed range.
- Got: one row per marker (~8/s) with no CPU % in the row label; the percentage is only in `marker info` or `--json` `data.cpuPercent`.
- Workaround: `--json` and a Python script bucketing `data.cpuPercent` per second.
