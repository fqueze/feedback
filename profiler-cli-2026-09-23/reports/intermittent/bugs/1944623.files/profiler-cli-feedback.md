## Question: "was the machine's CPU user or kernel time during this window, and was one core pinned?"

- Command: `profiler-cli thread markers --session 1944623-1 --search "name:CPU Use" --list --limit 0 --json` over a `zoom push` of the hang, then a Python script (`cpu_split.py`) bucketing `cpuPercent` / `user_pct` / `system_pct` per 1-20 s.
- Expected: something like `counter info` for the resource-usage profile's CPU markers: per-bucket median and minimum of total, user and system CPU, ideally also as "logical cores busy" (the profile meta knows the core count).
- Got: the resource-usage profile has no counters (`counter list`: "No counters in this profile"); the CPU data is only in ~16,000 `CPU Use` interval markers, whose aggregate view gives durations, not values.
- Workaround: the script. It showed one logical core busy in system time for the whole 5-minute hang, which was the key observation of this report.

## Question: "how much CPU did each thread use during the zoomed range"

- Command: `profiler-cli zoom push 175,510 --session 1944623-2` then `profiler-cli profile info` / `profiler-cli thread list`.
- Expected: CPU per thread within the zoomed range.
- Got: the full-profile CPU totals, unchanged by the zoom (the header says the view is zoomed).
- Workaround: `counter info c-N` for the process CPU counter, which does respect zoom.

## Question: "was the parent process busy during the hang?" — `counter info` percentages disagree with the raw counts

- Command: `profiler-cli counter info c-0 --session 1944623-2` (processCPU of the parent, profile `profile_shutdown_hang_928.json` of task FlAFdUWsQ7qbgeQYGvKXLA), unzoomed, then zoomed to 0-170 s and 175-510 s.
- Expected: buckets proportional to the process's CPU time.
- Got: 12-19% per bucket before the hang, 2.8% flat during it. The raw `counters[].samples.count` sums are 6.8e8/s before (100-165 s) and 2.9e6/s during (175-510 s): a 230x drop, not 5x; and every profiled parent thread has `threadCPUDelta` 0 during the hang. The 2.8% read as "a quarter of a core busy" on an 8-core machine, which was wrong.
- Workaround: summed `threadCPUDelta` and the counter's `count` from the downloaded profile JSON in Python.
