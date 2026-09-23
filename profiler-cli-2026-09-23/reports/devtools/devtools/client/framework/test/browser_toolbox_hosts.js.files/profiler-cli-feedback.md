## Question: how busy was the whole machine between two times (resource-usage profile)?

- Command: `profiler-cli counter list --session browser_toolbox_hosts.js-1` on a `profile_resource-usage.json`
- Expected: a CPU counter (the machine-wide CPU use the resource-usage profile records), so `zoom push 656.5,1026` + `counter info` would give the average.
- Got: `No counters in this profile.` The CPU use is stored as ~16k `CPU Use` interval markers (`cpuPercent` field), and `thread markers --search 'name:CPU Use'` only aggregates their durations, not the field values.
- Workaround: `thread markers --search 'name:CPU Use' --list --limit 0 --json` and a Python script averaging `data.cpuPercent` per window.
- What would have answered it: treating the `CPU Use` markers of resource-usage profiles as a counter (or a field-stats summary, e.g. mean/max of a numeric field, in `thread markers` aggregate output under a zoom).

