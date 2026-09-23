## Question: at what exact time did each line of the test's log happen?

- Command: `profiler-cli thread markers --category Test --search browser_dbg-windowless-workers.js --list --limit 0 --session <s>`
- Expected: timestamps precise enough to order the test's log lines against other markers (ms).
- Got: `t=1m46s` for eight consecutive INFO lines spanning 500 ms; the order against other threads' markers cannot be read.
- Workaround: `--json` and reading `flatMarkers[].start`.
- What the default output could show: milliseconds (e.g. `t=106.128s`) in `--list` mode.

