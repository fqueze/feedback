## Question: how busy was the machine during a 370 s hang (resource-usage profile)

- Command: `profiler-cli load <.../GL23UCGPTvud7fh7DlmQZw/.../profile_resource-usage.json> --session 1470951-1`, then `profiler-cli profile info --session 1470951-1`.
- Expected: some view of machine CPU over time.
- Got: "CPU activity over time: No significant activity." and `counter list` "No counters in this profile." Yet the profile has 4525 `CPU Use` markers whose payload shows a steady ~28% machine CPU during the whole hang.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` plus a script averaging `data.cpuPercent` per 30 s bucket.
- What could have shown it: `profile info` (or a `thread markers --summary-field cpuPercent --bucket 30s`) summarising the CPU Use markers of mozharness resource-usage profiles as a time series.
