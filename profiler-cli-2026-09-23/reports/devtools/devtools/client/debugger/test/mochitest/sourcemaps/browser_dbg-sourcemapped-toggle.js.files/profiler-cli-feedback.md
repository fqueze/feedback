## Question: "was the machine saturated during this test?" (resource-usage profile)

- Command: `profiler-cli counter list --session <s>` on `profile_resource-usage.json` -> "No counters in this profile."; `profile info` -> "CPU activity: No significant activity."
- Expected: a machine-CPU summary for the zoomed range (mean / p90 / max), since the CPU track of a resource-usage profile is what the brief says answers "slow machine or not".
- Got: CPU use lives in `CPU Use` markers (several per 100 ms, `cpuPercent` field). `thread markers --search "name:CPU Use"` only lists them; no aggregate over the field.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` piped to a python script averaging `data.cpuPercent` per zoom range.
- What could have shown it: `profile info` / `counter info` treating resource-usage `CPU Use` markers as the machine CPU track, or `thread markers --group-by` offering stats (mean/max) of a numeric field.
