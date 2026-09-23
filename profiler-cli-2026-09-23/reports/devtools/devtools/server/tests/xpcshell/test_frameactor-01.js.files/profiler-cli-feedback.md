## Marker times in the default `--list` output are rounded to the second

- Question: when exactly did this test start, relative to the first launch failure and to its own "will retry" line (they are 32 ms and 1.5 s apart)?
- Command: `profiler-cli thread markers --session <s> --search test_frameactor-01 --list --limit 0` on the LKys8j8i resource-usage profile (3m37s long).
- Expected: start times precise enough to order markers within one second (e.g. `t=133.132s`).
- Got: `t=2m13s` for the test start, the `will retry` line, and every launch-failure warning alike; `marker info` also prints `Time: 2m13s - 2m58s`.
- Workaround: `--list --json` and read `start` (ms).

## No summary of the machine's CPU use in a resource-usage profile

- Question: was the machine saturated in the seconds before the breakdown?
- Command: `profiler-cli profile info`, `counter list` ("No counters in this profile"), `thread samples --include-idle` in a zoom ("No samples in the current view") on the LKys8j8i resource-usage profile.
- Expected: the `CPU Use` series summarized over the view (per-bucket averages), like `counter info` does for counters.
- Got: CPU use exists only as ~2,100 `CPU Use` interval markers with a `cpuPercent` field; `profile info` reports "No significant activity".
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a script to print or bucket `cpuPercent`.
