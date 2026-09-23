## Question: the exact time of each line of the test's log
- Command: `profiler-cli thread markers --session <s> --category Test --search <test file> --list --limit 0`
- The list shows `t=10m10s` for 18 consecutive markers spanning 100 ms, so ordering against other markers (DB blockers at 10m10s) needed sub-second times.
- Workaround: `--json` and print `flatMarkers[].start`.
- Could show: milliseconds in `t=` when the view spans minutes (e.g. `t=10m9.809s`), or a `--precise-times` flag.

## Question: the ordered list of test outcomes in a resource-usage profile
- Command: `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0`
- `name:test` also matches every payload containing "test", so the `test` markers were drowned among INFO/PASS/output markers.
- Workaround: `--json` and filter `name == "test"` in Python.
- Could show: an exact-name match (e.g. `name=test`), or a marker-name-only filter.
