## Question: in what order, and how far apart, did the test's log lines happen within one second?

- Command: `profiler-cli thread markers --session <s> --search ExperimentalAsync --list --limit 0` on a resource-usage profile (Android emulator mochitest job, task QbjUQRIsQ-mt-EGlKqypvw).
- Expected: rows in chronological order, with a timestamp precise enough to separate them.
- Got: every row of the test's ~450 ms of log printed as `t=3m53s`, and the rows not in time order: all PASS markers first, then the INFO markers ("Loading buffer2", "SourceBuffer buffered ranges grew ...") that were interleaved with them. The last line before the stall (m-145) was listed in the middle.
- Workaround: `--json`, sort `flatMarkers` by `start`, print ms offsets. Showing ms (or at least sub-second precision when neighbouring rows share a second) and sorting by start would have answered it.

## Question: how busy was the machine during this test's timeout, compared with while tests ran?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use"` after `zoom push <test marker>`; also `profile info`.
- Expected: mean / percentile CPU % (and user vs system share) of the CPU Use markers in the zoomed range.
- Got: marker count and interval durations only; `profile info` says "CPU activity over time: No significant activity" because the resource-usage profile carries CPU in CPU Use markers, not samples.
- Workaround: `--list --limit 0 --json`, then average `data.cpuPercent` / `user_pct` in Python. A per-range summary of the CPU Use payload (mean, p10/p50/p90, user/system) in the aggregate view would have answered it.

## Correction to the first entry above

- The rows were in chronological order after all: the marker handles (m-76..m-80 after m-144) are not monotonic in time, which made it look out of order. The remaining problem is only the one-second rounding of `t=`: ~25 log lines within 450 ms all read `t=3m53s`, so their spacing needed `--json`.
