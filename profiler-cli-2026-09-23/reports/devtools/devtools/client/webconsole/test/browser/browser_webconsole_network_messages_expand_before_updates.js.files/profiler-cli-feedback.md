## Question: "was the machine busy while this test waited?" (resource-usage profile)

- Commands: `profiler-cli profile info` on `profile_resource-usage.json` says "CPU activity over time: No significant activity", and `profiler-cli counter list` says "No counters in this profile".
- The machine CPU is there, but only as `CPU Use` interval markers (`cpuPercent`), so I needed `thread markers --search "name:CPU Use" --list --json` plus a script to bucket them over the test marker's range.
- What could have answered it: have `profile info`/`zoom push m-<test>` summarize the `CPU Use` markers as CPU over time (mean/max per bucket), or expose them as a counter.
