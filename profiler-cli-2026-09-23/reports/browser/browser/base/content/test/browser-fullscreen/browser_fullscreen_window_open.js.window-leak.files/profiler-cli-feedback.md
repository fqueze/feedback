## Machine CPU use over a time range of a resource-usage profile

- Question: how busy was the machine between a test's end and the harness's leak check (ANo8DnZsR7O1Cqqr13ZBGA, 143–164 s)?
- Commands: `profiler-cli counter list --session browser-window-leak-ru1` → "No counters in this profile". `profiler-cli profile info` → "CPU activity over time: No significant activity." `profiler-cli thread markers --search 'name:CPU Use' --list --limit 0` lists 211 rows with times and durations but no CPU percentage.
- Expected: the CPU % in each `CPU Use` row of the list (it is the marker's only interesting field), or `profile info` / `counter list` summarizing the `CPU Use` markers as the time series they are.
- Workaround: `profiler-cli marker info m-187..m-392 --json` and a Python script bucketing `cpuPercent` per second.
