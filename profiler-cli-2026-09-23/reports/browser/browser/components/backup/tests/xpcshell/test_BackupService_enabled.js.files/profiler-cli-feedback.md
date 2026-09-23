## Question: was the machine CPU-saturated during this window of a resource-usage profile?

- Command: `profiler-cli counter list --session <s>` on `profile_resource-usage.json` (xpcshell job)
- Expected: a CPU counter with its "over time" buckets, as `counter info` gives for other profiles.
- Got: "No counters in this profile." CPU exists only as ~22,000 `CPU Use` interval markers; `thread markers --search "name:CPU Use"` lists them one by one, and `marker info` shows one sample.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python script to compute the duration-weighted CPU% per 30 s bucket.
- What would have answered it: expose the `CPU Use` markers as a counter (or a `--summary-over-time` on markers with numeric fields).
