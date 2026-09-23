## `thread markers --list` time column rounds to whole seconds past 1 minute

- Command: `profiler-cli thread markers --category Test --search browser_aboutNetError.js --list --limit 0 --session browser_aboutNetError.js-1` (a 1m14s profile)
- Expected: a start time precise enough to order markers, e.g. `t=68.392s`.
- Got: `t=1m8s` for every marker in the last test, so the TEST-PASS lines, the network loads and the IPC messages of one second could not be ordered or compared, and `profile markers --search` had the same column.
- Workaround: `--json` and reading `start` from `flatMarkers` / `markers` with a python one-liner. The question was "in which order did these happen, to the millisecond", which the default output could have answered with milliseconds in the time column.
