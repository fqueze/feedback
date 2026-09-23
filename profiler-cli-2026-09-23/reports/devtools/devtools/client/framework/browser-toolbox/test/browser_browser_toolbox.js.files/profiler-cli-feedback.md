## Machine CPU over a time range, in a resource-usage profile

- Command: `profiler-cli counter list --session <s>` and `profiler-cli profile info --session <s>` on `.../XIwvIvW7QWew824k8dUGGQ/runs/0/artifacts/public/test_info/profile_resource-usage.json`
- Question: "was the machine busy during this 6-minute hang, compared with the rest of the job?"
- Expected: CPU over time for the zoomed range (like `counter info`'s "over time" section), or `profile info`'s "CPU activity over time".
- Got: `No counters in this profile.` and `CPU activity over time: No significant activity.` The data is there, as 3,813 `CPU Use` markers (one per 100 ms, `cpuPercent` field) in the test's range.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json`, then a Python script bucketing `data.cpuPercent` per range. Note the first dump silently kept an earlier `zoom push`, so the "baseline" windows came out empty until I re-ran it after `zoom clear`.
