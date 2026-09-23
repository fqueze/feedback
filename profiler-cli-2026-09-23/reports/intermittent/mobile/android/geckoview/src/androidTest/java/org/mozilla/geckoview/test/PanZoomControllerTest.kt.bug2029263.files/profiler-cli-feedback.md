# profiler-cli feedback (PanZoomControllerTest.kt, bug 2029263)

## CPU Use markers list without their values

- Command: `profiler-cli zoom push m-59` (the failing `test` marker in a resource-usage profile), then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0`
- Expected: one row per 100 ms sample with its CPU percentage, answering "was the machine saturated while this test ran".
- Got: 16 rows that say only `CPU Use  t=11m45s  100ms`. The percentage is in the payload, and there is no hint of it in the row.
- Workaround: `--json` and read `flatMarkers[].data.cpuPercent`. `marker info m-a m-b ...` also works, but it prints about 20 lines per marker.
- Question: "the machine's CPU use across one test's interval". A `CPU Percent` column in the list rows for CPU markers would have answered it, or a summary line (min/avg/max cpuPercent) in the `byType` stats.
