## No way to summarize a payload field over a range ("what was machine CPU during this test?")
- Question: the machine's CPU use while one test ran, from a resource-usage profile's `CPU Use`
  markers (it has no counters: `counter list` is empty).
- Command: `profiler-cli zoom push 180,225` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json`, piped to a Python script to average `cpuPercent` per 3 s bucket.
- Expected: something like `thread markers --search "name:CPU Use" --stats cpuPercent` giving
  mean/min/max (and optionally per-bucket) of a numeric field within the zoom.
- Got: 365 rows to read, or JSON to script over.
