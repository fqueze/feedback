## Exact start time of a marker past t=60s

- Command: `profiler-cli marker info m-1 m-3 m-7 --session test_new_source-02.js-2`
- Expected: start times with millisecond precision, to compare a test's start with a launch failure 1.6 s earlier.
- Got: `Time: 2m13s - 2m58s (45.041s)` and `Time: 2m12s (instant)`: past one minute, times are rounded to the second (the duration keeps its ms).
- Workaround: `--json` and read `start`. The default output could print `2m13.260s`.
