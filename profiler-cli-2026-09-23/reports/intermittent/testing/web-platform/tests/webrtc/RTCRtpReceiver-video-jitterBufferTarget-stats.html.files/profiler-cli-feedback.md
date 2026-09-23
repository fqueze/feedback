## Question: "was the machine busy while this test ran?" (resource-usage profile)

- Commands: `profiler-cli profile info --session jbt-1` then `profiler-cli counter list --session jbt-1` on `FHzFGfukRZC9V0MHgJ5RIQ/runs/0/artifacts/public/test_info/profile_resource-usage.json`.
- Expected: a CPU track/summary over time, or over the current zoom.
- Got: `CPU activity over time: No significant activity.` and `No counters in this profile.` The CPU data exists, but only as ~10 `CPU Use` markers per second with `cpuPercent` payloads, so the overview reads as an idle machine whatever the data says.
- Workaround: `zoom push m-2` (the test marker), `thread markers --search "name:CPU Use" --list --limit 0 --json`, then a script averaging `data.cpuPercent` (mean 3.6%, max 36% over the 15 s test).
- What it could show: for resource-usage profiles, a mean/max/per-second summary of `CPU Use` (and `Memory`/`IO`) markers in the current zoom, in `profile info` or `thread markers` aggregate output.
