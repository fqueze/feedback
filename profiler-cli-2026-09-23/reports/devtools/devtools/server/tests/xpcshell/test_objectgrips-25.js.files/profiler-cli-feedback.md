## How long after the test's start did the harness say "will retry"? (profiler-cli 2026-09-22)

- Command: `profiler-cli thread markers --session <s> --search objectgrips-25 --list --limit 0`
- Expected: start times precise enough to tell a 36 ms gap (process never launched) from a multi-second one.
- Got: both markers print as `t=2m13s`; the list rounds starts to the second, and `marker info` does too for instants (`Time: 2m13s (instant)`).
- Workaround: `--json` and read `flatMarkers[].start` (133338.908 vs 133374.908).
- What would have answered it: millisecond start times in `--list` when the listed markers are within a second or so of each other, or a delta-from-previous column.
