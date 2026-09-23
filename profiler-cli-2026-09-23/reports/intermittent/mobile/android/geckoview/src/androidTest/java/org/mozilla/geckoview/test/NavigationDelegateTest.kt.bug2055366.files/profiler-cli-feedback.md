## CPU Use values not shown in `thread markers --list`

- Question: "what was the machine's CPU use during this test's 6.6 s?"
- Command: `profiler-cli zoom push m-59` then `profiler-cli thread markers --search "CPU Use" --list --limit 0`
- Expected: each row with its CPU percent (the only field that matters for this marker type).
- Got: 68 rows of `CPU Use t=10m53s 100ms` with no value; had to use `--json` and a Python script over
  `.flatMarkers[].data.cpuPercent`. A per-row value, or `counter`-style summary (min/median/max) of the
  CPU Use markers in the zoomed range, would have answered it directly.

## (review) Same question again: CPU Use values within a test's range

- Command: `profiler-cli thread markers --search "CPU Use" --list --limit 0 --session <s>`, after loading a link whose range is the test's `test` marker.
- Got the same result: rows without `cpuPercent`. I needed `--json` plus a script again to check the values the report quotes.
