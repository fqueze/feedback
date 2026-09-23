## Question: was the machine busy or idle while this test hung? (resource-usage profile)

- Command: `profiler-cli profile info --session <s>` on an Android `profile_resource-usage.json`, then `profiler-cli zoom push m-48` + `thread markers --search "name:CPU Use"`.
- Expected: CPU use of the machine over the zoomed range (avg/max of the `CPU Use` markers' `cpuPercent`), since that is the only CPU data this profile type has.
- Got: `profile info` says "CPU activity over time: No significant activity" and "Platform: Unknown" (the profile has no samples); `thread markers` aggregates only durations/rates, not payload values.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python script averaging `data.cpuPercent` over time buckets. A per-range summary of numeric marker fields (or `profile info` using CPU Use markers when there are no samples) would have answered it directly.
