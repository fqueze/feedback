## "Was the machine saturated at time T?" on a resource-usage profile

- Command: `profiler-cli profile info --session test_queue.js-1` and `profiler-cli counter list --session test_queue.js-1` on an xpcshell `profile_resource-usage.json`
- Expected: a machine-CPU-over-time summary, since the profile does carry `CPU Use` markers every ~100 ms.
- Got: "CPU activity over time: No significant activity." and "No counters in this profile.". `thread markers --search "name:CPU Use" --list` prints the markers without their CPU Percent value, so you need `marker info` on each one.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python script to average `data.cpuPercent` per 5 s bucket. What the output could have shown: a CPU-percent sparkline or bucket table from the `CPU Use` markers in `profile info`, or the value as a column in the `--list` rows.
