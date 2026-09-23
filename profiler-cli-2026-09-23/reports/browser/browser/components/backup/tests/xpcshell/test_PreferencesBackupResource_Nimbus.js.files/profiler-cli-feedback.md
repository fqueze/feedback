## Question: how busy was the machine over a time range (resource-usage profile)?
- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list` after `zoom push 105,130`
- Expected: the CPU percent per marker in the list (or a per-second summary), since that is the only payload of interest.
- Got: rows with time and duration only; the `CPU Percent` field needs `marker info` per marker. No counters in the profile either (`counter list` says none).
- Workaround: `--json` and a python script averaging `data.cpuPercent` per second. A per-bucket summary of CPU Use markers (like `counter info`'s "over time") would answer it.

