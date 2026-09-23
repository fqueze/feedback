## `thread markers --list` rounds start times to whole seconds

- Command: `profiler-cli thread markers --search test_stepping-17 --list --limit 0 --session <s>` on a 3m37s resource-usage profile.
- Expected: start times precise enough to order markers that are milliseconds apart. Here the test starts at 133.535 s and its `will retry` comes 36 ms later.
- Got: `t=2m14s` for both, and for the `marker info` of each.
- Workaround: `--json` and read `flatMarkers[].start`.
