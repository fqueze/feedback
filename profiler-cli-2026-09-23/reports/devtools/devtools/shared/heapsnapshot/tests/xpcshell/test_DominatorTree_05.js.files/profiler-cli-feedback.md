## Machine CPU over time in a resource-usage profile needs a script

- Command: `profiler-cli profile info --session test_DominatorTree_05.js-1` on `.../PyUxuOpdQj2b3T3XyQOKpg/runs/0/artifacts/public/test_info/profile_resource-usage.json`
- Expected: the CPU use of the machine over time (the profile has 1232 `CPU Use` markers with `cpuPercent`).
- Got: `CPU activity over time: No significant activity.` and `No counters in this profile.`, since CPU is only in markers.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` then a Python script averaging `cpuPercent` per 5 s bucket.
- Question: "was the machine busy or idle while these tests timed out?" A per-bucket summary of the `CPU Use` markers in `profile info` (or `counter`-like output for them) would answer it.

## Distribution of marker start times / durations needs a script

- Command: `thread markers --search TIMEOUT --list --limit 0 --json` + Python
- Question: "did these 1,150 TIMEOUTs start together, and did they all last the same?" The aggregate view gives min/avg/max duration per name but no start-time histogram; a `--group-by` on a time bucket, or a start-time sparkline per group, would answer it.
