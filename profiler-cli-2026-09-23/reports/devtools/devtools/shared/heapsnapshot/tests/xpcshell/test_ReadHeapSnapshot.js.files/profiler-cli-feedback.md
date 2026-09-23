## When did a repeated marker start and stop, and how many were there?

- Command: `profiler-cli thread markers --session test_ReadHeapSnapshot.js-1 --search "will retry" --list --limit 15`
- Expected: the match count plus the time of the first and last match. The question was how long the cascade of instant launch failures lasted.
- Got: the count (1476) and the first 15 rows. The last match's time was only available through `--json` and a Python script (`min`/`max` over `flatMarkers[].start`).
- Could show: a `first t=… last t=…` line next to "N markers (filtered from M)" in `--list` output, or in the aggregate view for a `--search`.
