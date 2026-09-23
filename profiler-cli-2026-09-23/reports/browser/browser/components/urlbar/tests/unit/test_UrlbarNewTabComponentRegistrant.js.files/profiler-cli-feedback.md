## Question: was the machine saturated during this one test's run?
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` over a zoomed range, then a Python script to average `cpuPercent` per second.
- The default output lists one row per 50–250 ms CPU marker without the percentage, so the answer ("100% every second from t=1275 to 1291 s") needed a script. A `--summary`/per-bucket view of CPU Use markers in the current zoom (like `counter info`'s "over time") would answer it directly.

## Question: how many tests ran concurrently with this one, and did it start in a burst?
- Command: `thread markers --search "name:test" --list --limit 0 --json` + script to count overlapping `test` interval markers.
- A concurrency count at a timestamp (or per bucket) for interval markers would answer it without a script.

## `marker info m-a m-b … --json` records have no `handle`
- Command: `profiler-cli marker info m-115 m-118 … --json`
- Got: records with `handle: null`, so matching records back to handles relies on order.
