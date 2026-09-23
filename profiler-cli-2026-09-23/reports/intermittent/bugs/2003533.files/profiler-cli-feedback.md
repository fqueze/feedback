## Was the machine saturated during this test?

- Command: `profiler-cli thread markers --search "CPU Use" --list --limit 0` after `zoom push m-64` (the test's `test` marker) on a resource-usage profile.
- Expected: the CPU percent of each 100 ms sample in the list, since that is the only thing a `CPU Use` marker carries.
- Got: 33 rows of `CPU Use  t=7m52s  100ms` with no value; `marker info` shows it one marker at a time.
- Workaround: `--json` and a Python one-liner over `.flatMarkers[].data.cpuPercent`. The list could show the marker's schema label fields (cpuPercent) inline, or `counter`-style min/avg/max over the zoom.
