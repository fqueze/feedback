## `thread markers --list` prints times rounded to the second

- Command: `profiler-cli thread markers --category Test --search browser_grids_grid-list-on-iframe-reloaded --list --limit 0 --session <s>`
- Expected: a start time precise enough to order markers that are milliseconds apart (e.g. `66.405s`).
- Got: `t=1m6s` for every marker between 66.0 and 66.99 s, so the click, the state change and the failure all read as the same time. `marker info` prints the same rounded `Time: 1m6s`.
- Workaround: `--json` and read `flatMarkers[].start`.
- Question it failed to answer: "in what order, and how far apart, did the test's log lines happen?"

