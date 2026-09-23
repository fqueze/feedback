## `--max-duration 0` drops instant markers

- Command: `profiler-cli zoom push 91.9820,91.9832 --session test_symbols-02.js-1` then `profiler-cli thread markers --list --limit 40 --max-duration 0 --session test_symbols-02.js-1`
- Question: which log lines (instant markers) fall inside a 1 ms window, without the long interval markers that overlap it (the parallel phase, 900 test markers).
- Expected: the instant markers only.
- Got: `0 markers (filtered from 921)`.
- Workaround: `--list --limit 0 | grep instant`. An `--instant` flag, or instants counting as duration 0, would answer it.

## Marker times past 1 minute have no sub-second precision

- Command: `profiler-cli marker info m-1 m-3 m-7 --session test_symbols-02.js-2`
- Question: exact start of a marker at t>60 s, to compare it with another one 2 s earlier.
- Got: `Time: 2m14s - 2m59s (45.030s)`, and `2m14s` for both the test start and its `will retry` line 38 ms later.
- Workaround: `--json` and reading `start`. Showing `2m13.554s` in `marker info` would do.
