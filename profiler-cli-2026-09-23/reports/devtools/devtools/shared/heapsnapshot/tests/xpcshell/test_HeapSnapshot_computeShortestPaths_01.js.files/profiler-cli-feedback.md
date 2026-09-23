## Question: what was the machine's CPU use over a time window (resource-usage profile)?

- Command: `profiler-cli thread markers --session <s> --search "CPU Use"` (then `--list --limit 0 --json` and a Python script averaging `data.cpuPercent` weighted by duration per 5 s bucket).
- Expected: `profile info` / `counter list` to show machine CPU over time for a resource-usage profile.
- Got: `counter list` says "No counters in this profile", `profile info` says "No significant activity"; the CPU data only exists as 1,232 `CPU Use` markers, whose default output is just a count.
- Could have shown: a duration-weighted average of `cpuPercent` for the current zoom, or a bucketed "over time" view like `counter info` has.

## Question: how many tests ended with each status, among those starting in a time window?

- Command: `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0 --json` + script over `data.status` and `start`.
- Expected: `--group-by field:status` under a `zoom push` to answer it, but a zoom includes interval markers that merely overlap the window, so "started in the window" needs a script.
- Could have shown: an option to keep only markers starting inside the zoom.
