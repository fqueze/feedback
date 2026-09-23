## CPU use over a time window, in a resource-usage profile

Question: what was the machine's CPU use, second by second, while one test ran (resource-usage profile, no counters)?
Command: `profiler-cli thread markers --session <s> --search 'name:CPU Use' --list --limit 0` (after `zoom push`)
Expected: the CPU percent in each row, or a summary (mean/min/max) of the `cpuPercent` field over the zoomed range.
Got: rows with only time and duration; the CPU percent is only in `marker info` or `--json` `fields[]`.
Workaround: `--json` piped to a python script bucketing `cpuPercent` per 5 s.

