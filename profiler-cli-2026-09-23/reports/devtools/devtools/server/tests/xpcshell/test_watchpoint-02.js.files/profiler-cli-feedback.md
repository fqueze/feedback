## Question: how many ms between a test's start and its "will retry" line?

- Command: `profiler-cli thread markers --session <s> --search test_watchpoint-02 --list --limit 0`
- Expected: start times precise enough to tell a 36 ms gap from a 1 s gap.
- Got: `t=2m14s` for both the `test` marker and the `will retry` INFO marker; second-level rounding hides the gap that says the process never launched.
- Workaround: `--json` and read `flatMarkers[].start` (133582.908 vs 133618.908).
- What the output could show: millisecond start times in `--list` (e.g. `t=2m13.583s`), at least when the list is short or `--search` is given.

## Question: what was the machine's CPU use, per 5 s, around a timeout?

- Command: `profiler-cli thread markers --session <s> --search "CPU Use" --list --limit 0` (zoomed to 125–180 s)
- Expected: the CPU percentage per marker, or an aggregate over the zoomed range.
- Got: 500+ rows of `CPU Use  t=2m12s  125ms` with no percentage; the value is only in `marker info` or `--json` `data.cpuPercent`. `counter list` says there are no counters.
- Workaround: `--json`, then a script computing a duration-weighted average of `data.cpuPercent` per 5 s bucket.
- What the output could show: the `CPU Percent` field inline in the `--list` row, and/or a time-bucketed summary for `CPU` markers like `counter info` gives for counters.
