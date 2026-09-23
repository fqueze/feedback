## Question: what was the machine's CPU use during one test?

- Question: in a resource-usage profile, "was the machine saturated while test X ran?"
- Command: `profiler-cli zoom push 2499,2596` then `thread markers --search "name:CPU Use" --list --limit 0 --json`, and a Python script to average `data.cpuPercent` over the 972 markers.
- The default output of `thread markers` for `CPU Use` gives count and interval stats only. A mean/median/p90 of the `cpuPercent` field over the zoomed range (e.g. a `--stats-field cpuPercent`, or showing numeric field stats in the by-name summary) would have answered it without JSON.

## Question: how long did each subtest take?

- Question: "which subtests eat the 98 s of this test?"
- Command: `thread markers --category Test --search <file> --list --limit 0 --json`, then a script pairing `Entering test X` / `Leaving test X` markers and subtracting.
- The text list shows times rounded to the second (`t=41m3s`), too coarse for 0.4 s subtests, and has no durations. Either millisecond times in `--list`, or a per-subtest duration view for mochitest logs, would answer it.
