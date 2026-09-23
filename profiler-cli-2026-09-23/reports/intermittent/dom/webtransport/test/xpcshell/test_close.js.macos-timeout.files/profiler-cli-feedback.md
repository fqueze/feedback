## Question: what was the machine's CPU use while one test ran?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` after `zoom push <test marker>` on a resource-usage profile.
- Expected: the CPU percentage per row, or a summary (mean/min/max of `cpuPercent`) over the zoomed range.
- Got: 481 rows showing only `CPU Use  t=3m55s  100ms`, no value. `counter list` says "No counters in this profile".
- Workaround: `--json`, then a Python script averaging `data.cpuPercent` in 3 s buckets.
- What could have shown it: the list row could print the marker's main field (CPU Percent), or `thread markers --search "name:CPU Use"` aggregate could give min/mean/max of numeric fields.

## Question: what did the main thread run after the last sample (a timed-out test's final moments)?

- Command: `profiler-cli thread markers --session <s> --list --limit 0 --search "name:Runnable,-name:DummyEvent"` on a per-test timeout profile whose samples end at 30.531 s.
- Expected: every matching marker, including the instants recorded between the last sample and the dump (here at 30.814 s and 30.818 s, where the stalled connection finally completed).
- Got: the list silently stops at the last sample; the markers after it only show after `zoom push 0,32` (which warns "Range extends outside the profile duration"). Interval markers that *start* before the end are listed, instants after it are not, and nothing says some were left out.
- Workaround: `zoom push 0,32` (a range past the end), then list.
- What could have shown it: the full view could include markers past the last sample, or print "N markers after the last sample are outside the view".
