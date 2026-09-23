## Marker times lose sub-second precision in profiles over a minute

- Question: at what exact time did each of the test's log lines happen, so I can `zoom push` around one? (profile of browser_dbg-blackbox-all.js, 1m59s long)
- Command: `profiler-cli thread markers --session <s> --category Test --search browser_dbg-blackbox-all --list --limit 0`
- Expected: `t=100.410s`-style times, as in shorter profiles.
- Got: `t=1m39s`, `t=1m40s`, `t=1m46s` for every row, so a 6 s window could not be placed. `profile markers` and `DOMEvent` lists had the same rounding.
- Workaround: `--json` and reading `flatMarkers[].start`. The default output could show milliseconds (`t=1m40.414s`).
