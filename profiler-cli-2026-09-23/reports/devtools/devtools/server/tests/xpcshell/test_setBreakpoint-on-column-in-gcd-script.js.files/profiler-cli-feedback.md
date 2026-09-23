## Question: exact start time of a marker past the first minute

- Command: `profiler-cli thread markers --session <s> --search column-in-gcd-script --list --limit 0` and `profiler-cli marker info m-1 m-3 --session <s>`
- Expected: millisecond timestamps (e.g. t=133.436s), needed to compute that the test gave up ~34 ms after it started, and that it started ~1.8 s after the first launch failure.
- Got: `t=2m13s` for both the test start and the "will retry" INFO; `marker info` prints `Time: 2m13s - 2m58s (45.038s)`. Below 1 min the list shows `t=46.815s`, so precision drops from ms to seconds past 60 s.
- Workaround: `--list --json` and read `.flatMarkers[].start`.
