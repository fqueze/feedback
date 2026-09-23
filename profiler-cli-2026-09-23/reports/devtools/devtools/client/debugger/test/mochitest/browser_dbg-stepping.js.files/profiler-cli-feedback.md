## `zoom push` silently misreads the `7m36s` time format profiler-cli itself prints

- Command: `profiler-cli zoom push 7m36s,7m50s --session browser_dbg-stepping.js-1` (on a 30-minute resource-usage profile, whose marker lists print times as `t=7m27s`).
- Expected: a zoom to 7m36s-7m50s, or an error saying only seconds are accepted.
- Got: `Pushed view range: ts-1 (7s) to ts-1 (7s) (duration: 0s)`, exit 0. Every following query would have run on an empty range.
- Workaround: convert to seconds by hand (`zoom push 447,1047`).

## Question: "was the machine busy or idle during this test?" on a resource-usage profile

- Command: `profiler-cli thread markers --search "name:CPU Use"` inside a `zoom push m-42` (the test's 10-minute `test` marker).
- Expected: some summary of the `cpuPercent` field over the range (mean/max, or per-bucket), since that is the whole point of those markers.
- Got: only the duration stats of the markers (`interval: min=92ms, avg=99.924ms`), which say nothing about CPU. `counter list` says "No counters in this profile".
- Workaround: `--list --limit 0 --json`, then a Python script bucketing `data.cpuPercent` per 15 s. A `CPU Use` field summary (or exposing these markers as a counter so `counter info` works) would have answered it directly.
