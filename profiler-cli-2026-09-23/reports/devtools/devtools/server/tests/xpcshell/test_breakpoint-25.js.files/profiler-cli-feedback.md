## Question: "exactly when did this marker start, past the first minute?"

- Command: `profiler-cli thread markers --session test_breakpoint-25.js-3 --search test_breakpoint-25 --list --limit 0`, and `profiler-cli marker info m-1 --session test_breakpoint-25.js-3`
- Expected: a start time precise enough to compare with another marker 1.4 s earlier (e.g. `2m13.078s`).
- Got: `t=2m13s` in the list and `Time: 2m13s - 2m58s (45.034s)` in `marker info`; under a minute the list shows milliseconds (`t=46.426s`). Two markers at 2m12s and 2m13s cannot be ordered or subtracted.
- Workaround: `marker info ... --json` and read `start`.
