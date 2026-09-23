## `thread markers --list` rounds times to whole seconds

- Command: `profiler-cli thread markers --category Test --search browser_tools_overflow.js --list --limit 0 --session ...`
- Expected: millisecond timestamps, to order the test's log lines and measure the gap before the timeout.
- Got: `t=1m48s` for every line of the last task (about 40 lines spanning 1 s), so neither ordering nor the gap could be read.
- Workaround: `marker info <m> --json` per marker, reading `start`.
