## Question: "was the machine saturated while this test ran?" in a resource-usage profile

- Command: `profiler-cli zoom push m-78 --session …` then `profiler-cli counter list` ("No counters in this profile") and `profiler-cli thread markers --search "name:CPU Use"`.
- Expected: the mean, min and max of CPU Percent over the zoomed range, or per N-second bucket, like `counter info` gives for counters.
- Got: CPU is only available as about 450 `CPU Use` markers per 45 s, each with a `cpuPercent` field. The aggregate view gives only duration stats, not field stats.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json`, then a Python script that buckets `data.cpuPercent` by `start`.
- What would have answered it: field-value stats (mean/min/max, or a sparkline) for numeric marker fields in the `thread markers` aggregate, or exposing resource-usage `CPU Use` as a counter.
