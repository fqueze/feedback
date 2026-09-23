## `thread markers --list` times too coarse to order markers

- Command: `profiler-cli thread markers --session test_blackboxing-03.js-1 --search "test_blackboxing-03" --list --limit 0` (resource-usage profile of ZtblArCLSx6O6KzfEkL1YQ, 2m23s long)
- Question: did this test start before or after the first child-launch failure, and how long after its start did "will retry" come?
- Expected: start times to the millisecond (or at least 0.1 s) in the list, since markers here are tens of ms apart.
- Got: `t=1m5s` for both the test start and the "will retry" INFO (33 ms apart); `marker info` also prints `Time: 1m5s - 1m50s`.
- Workaround: `--json` and read `flatMarkers[].start`.
