## "What was the machine's CPU use during this test?" needs a script

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` after `zoom push m-1` (the `test` marker of the failing junit test, resource-usage profile of WkWCjrpwTZqXCYlkP6fxCQ).
  - Expected: one row per sample with its `cpuPercent`, or a summary (min / median / max) of `cpuPercent` over the zoomed range.
  - Got: 78 rows of `m-N  CPU Use  t=20m40s  100ms  ✗` with no value at all; the percentage is only in `--json` `fields[]`. `profile info` says "No significant activity" and `counter list` says "No counters", so nothing else answers it either.
  - Question the default output could have answered: "was the host saturated while the test was stalled?". Printing the payload's `cpuPercent` in the `--list` row (and a per-range median in the aggregated view) would.
  - Workaround: `--json` and a Python script over `flatMarkers[].fields`.
