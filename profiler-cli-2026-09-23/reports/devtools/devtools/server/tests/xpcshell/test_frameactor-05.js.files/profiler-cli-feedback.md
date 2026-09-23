## `thread markers --list` drops sub-second precision past 60 s

- Command: `PROFILER_CLI_SESSION_OWNER=test_frameactor-05.js profiler-cli thread markers --session test_frameactor-05.js-3 --search test_frameactor-05 --list --limit 0`
- Question: how long after a test started did its "will retry" line come (tens of ms)?
- Expected: start times such as `t=65.378s`, or `t=1m5.378s`.
- Got: `t=1m5s` for both the test marker and the INFO marker, so the 24 ms gap between them is invisible. Before 60 s the same list prints `t=46.491s`.
- Workaround: `profiler-cli marker info m-N --json` and read `start`, one marker per call.
