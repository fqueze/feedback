## Question: "how busy was the machine over this window?" (resource-usage profile)
- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list` (after `zoom push`)
- Expected: the CPU percent in each row, as the marker's description.
- Got: rows with only name, time and duration, no value. `profiler-cli counter list` says "No counters in this profile", so there is no CPU track to read either.
- Workaround: `marker info` on individual handles, or `--list --json` with a script reading `data.cpuPercent`.
