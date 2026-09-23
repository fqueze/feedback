## Question: was the machine saturated while one test ran? (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` after `zoom push <test marker>`
- Expected: some summary of the CPU Percent field over the zoomed range (mean/min/max, or per-bucket), since `CPU Use` markers are the machine's CPU track.
- Got: 393 rows, each only "CPU Use  t=3m45s  100ms", without the CPU Percent value; `counter list` says "No counters", `profile info` says "No significant activity".
- Workaround: `--json` and a python script averaging `data.cpuPercent` over 5 s buckets (100% throughout).
- What could have shown it: the list row could show the marker's first field (CPU Percent), or `thread markers` aggregate stats could summarize numeric fields of the searched marker type.

## Question: how late did a 16 ms timer's callback run? (per-test profile, main thread)

- Command: `profiler-cli thread markers --session <s> --search "name:Runnable,-name:DummyEvent" --list --limit 0 --json`, then a python script measuring, for each `notify[UrlbarProviderPlaces.sys.mjs]:JS` runnable, the gap since the end of the previous runnable.
- Expected: some way to see the idle gap before a given runnable, or which timer a timer-callback runnable belongs to and when it was armed.
- Got: 4716 rows of runnables; nothing relates a timer callback to its arming, and no gap column. On this macOS xpcshell profile 291,039 `DummyEvent` runnables in 7.5 s (38,700/s) also drown every other runnable unless excluded.
- Workaround: the script above (median gap 60.6 ms for a 16 ms timer).
- What could have shown it: a "gap before" column in `--list`, or `--group-by field:name` stats including the inter-marker gap per group (the "Frequency Analysis" block has avgGap for a whole name, but not per `field:name` group).

## Question: how many other tests ran alongside this one? (resource-usage profile; reviewer)

- Command: `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0` after `zoom push <test marker>`
- Expected: the overlapping `test` markers summarized: how many at once (concurrency), and how many in total, excluding SKIP.
- Got: 261 rows, including the SKIP ones and the tests that merely started or ended inside the span; the report under review had misread this as "about 20 other tests".
- Workaround: `--json` and a script sampling concurrency once per second (10 at a time throughout, this one included).
- What could have shown it: for interval markers, a "concurrent: min/max" line in the aggregate stats of `thread markers` over the zoomed range.

## Empty call tree with no explanation (per-test profile; reviewer)

- Command: `profiler-cli thread samples-top-down --session <s> --include-idle` after `zoom push 21.050,21.070`
- Expected: "No samples in this range" (samples here are ~33 ms apart).
- Got: the header and `Top-Down Call Tree:` followed by nothing, which reads like a failure.
- Workaround: none needed once understood.
