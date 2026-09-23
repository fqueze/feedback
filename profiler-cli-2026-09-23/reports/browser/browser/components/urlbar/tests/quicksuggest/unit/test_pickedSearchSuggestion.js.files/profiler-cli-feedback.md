## Question: how busy was the whole machine, second by second, over a window?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0 --json` (resource-usage profile of an xpcshell job, zoomed to 50 s), then a Python script bucketing `data.cpuPercent` per 2 s.
- Expected: a way to see the machine CPU % over time in the current zoom without a script, e.g. `counter info`-style "over time" buckets for the `CPU Use` marker values, or the CPU track exposed as a counter.
- Got: the resource-usage profile has no counters (`counter list` → "No counters in this profile"), and `thread markers --list` prints one row per CPU Use marker without its CPU Percent field; `profile info` says "No significant activity".
- Workaround: script over `--json`.
