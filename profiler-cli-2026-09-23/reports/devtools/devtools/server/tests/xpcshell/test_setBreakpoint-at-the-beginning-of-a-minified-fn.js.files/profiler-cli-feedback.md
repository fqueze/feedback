## Exact time of one marker

- Command: `profiler-cli marker info m-3 --session setbp-begin-2`
- Expected: the marker's start time to the millisecond, to compute "73 ms after the test started".
- Got: `Time: 1m6s (instant)`, rounded to the second.
- Workaround: `profiler-cli marker info m-3 --json` and read `.start` (65684.915). `thread markers --list` also rounds to the second past 1 min (`t=1m6s`), while it shows `t=52.873s` below one minute.
