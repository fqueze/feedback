## Machine CPU over a time range, in a resource-usage profile
- Question: "was the machine saturated between t=150 s and t=238 s (while the test waited)?"
- Command: `profiler-cli profile info` and `profiler-cli counter list` on `profile_resource-usage.json`.
- Expected: the CPU activity summary, or a counter, built from the `CPU Use` markers the profile has one of every 100 ms.
- Got: "CPU activity over time: No significant activity." and "No counters in this profile.", which reads as an idle machine whatever the markers say.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a script averaging `data.cpuPercent` over the range. A `--range` summary (mean / max of `cpuPercent`) in `profile info` for these profiles would answer it.
