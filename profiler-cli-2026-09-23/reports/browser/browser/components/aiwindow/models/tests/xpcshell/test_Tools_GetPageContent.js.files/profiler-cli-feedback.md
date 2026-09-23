## Question: "was the machine saturated or idle around this test, and did other tests keep completing?"
- Profile: a CI xpcshell resource-usage profile (one thread, no counters; CPU is in 20,000 `CPU Use` interval markers).
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` then a Python script bucketing `cpuPercent` weighted by duration per 5-10 s, and counting `test` marker ends per bucket.
- What the default output could have shown: `counter info`-style "over time" buckets for CPU Use markers (min/avg/max CPU% per bucket over the current zoom), since the resource-usage profile stores CPU as markers rather than counters, so `counter list` says "No counters in this profile".
