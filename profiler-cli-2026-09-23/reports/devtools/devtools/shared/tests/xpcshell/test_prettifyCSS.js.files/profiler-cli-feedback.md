## Question: when exactly did this marker start (past the first minute)?

- Command: `profiler-cli marker info m-1 m-3 m-7 --session test_prettifyCSS.js-3` (and `thread markers --list`)
- Expected: millisecond timestamps, as for markers before t=60s (`t=47.040s`).
- Got: `Time: 1m6s - 1m51s (45.018s)` and `Time: 1m6s (instant)`: past one minute, times are rounded to the second, so the 2 s gap between the job's first launch failure (`1m4s`) and the test's start, and the 11 ms to its `will retry`, could not be read.
- Workaround: `marker info ... --json` and read `start`/`end` (65883.915, 65894.915, 63895.915 ms).
- Could have shown: `1m5.884s`, as it does for the duration.
