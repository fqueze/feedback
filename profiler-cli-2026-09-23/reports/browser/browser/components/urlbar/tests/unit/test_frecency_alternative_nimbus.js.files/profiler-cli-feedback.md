## Question: how busy was the machine while one test ran?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` on a resource-usage profile (after `zoom push 150,180`).
- Expected: the machine's CPU use over the zoomed range (mean/min/max), since "was the machine saturated when this test ran" is the first question on a timeout or shutdown hang.
- Got: one row per 130 ms sample with only the handle and duration in text; the percentage is only in `--json` `data.cpuPercent`, and "No counters in this profile" from `counter list`.
- Workaround: `--json` and a Python script averaging `data.cpuPercent` per time bucket. Exposing these CPU Use markers as a counter (so `counter info` summarises them under a zoom), or showing `cpuPercent` in the list row, would have answered it.
