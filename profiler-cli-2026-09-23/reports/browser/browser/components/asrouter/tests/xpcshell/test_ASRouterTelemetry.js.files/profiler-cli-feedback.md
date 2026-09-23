## Was the machine saturated while this test ran? (resource-usage profile)

- Command: `profiler-cli profile info --session <s>` on `profile_resource-usage.json` of an xpcshell job.
- Expected: some CPU-over-time summary, since the profile carries a `CPU Use` marker every ~130 ms with `cpuPercent`.
- Got: `CPU activity over time: No significant activity.` (it only looks at samples, and this profile has none). `counter list` says "No counters".
- Workaround: `zoom push <range>` + `thread markers --search "name:CPU Use" --list --limit 0 --json`, then a Python script bucketing `data.cpuPercent` in 5 s windows.
- What would have answered it: `profile info` (or `thread markers` for `CPU` type markers) printing the machine CPU % over time from the `CPU Use` markers of the resource-usage profile, respecting the zoom.
