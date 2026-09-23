## Question: how busy was the machine while the test was "running" (resource-usage profile)

- Command: `profiler-cli counter list --session test_ignore_caught_exceptions.js-1` on the resource-usage profile of task JOcVulCfRyynVd-aExDW1g.
- Expected: a machine CPU counter (with its "over time" buckets), as `counter info` gives for Gecko profiles.
- Got: `No counters in this profile.` Machine CPU exists only as ~1,200 `CPU Use` markers (one per ~100 ms, `CPU Percent` field).
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` piped into a Python script to average `CPU Percent` per 5 s bucket.
- What could have answered it: expose the resource monitor's `CPU Use` markers as a counter (or have `profile info` print a CPU-over-time line for this profile type; it currently says "No significant activity").
