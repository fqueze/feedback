## Question: how busy was the machine while one test ran (resource-usage profile)?

- Command: `profiler-cli zoom push m-62` (the test marker), then
  `profiler-cli thread markers --search "name:CPU Use" --list --limit 0`
- Expected: one row per CPU Use marker with its CPU percent.
- Got: rows with only name, time and duration; the CPU percent is only in `marker info`.
- Workaround: `profiler-cli marker info m-217 ... m-225 --json` and a script to pull `cpuPercent`.
  What would have answered it: the CPU percent in the list row (or the counter track for CPU in
  `counter info`, which this profile does not have: `counter list` prints "No counters").
