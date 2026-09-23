## Question: how long did each subtest take?

- Command: `profiler-cli thread markers --category Test --search browser_smartwindow_smartformfill_telemetry --list --limit 0 --session <s>`
- Expected: timestamps precise enough to subtract `Entering test X` from `Leaving test X`.
- Got: `t=41m7s`, `t=41m12s`. Once past one minute, times are rounded to whole seconds, which is too coarse for subtests of 2 to 8 s.
- Workaround: `--json` and a Python script over `flatMarkers[].start`.
- Would have helped: millisecond times in `--list` (e.g. `t=2467.343s`), or a `--durations-between "Entering test" "Leaving test"` style pairing.

## Question: machine memory over a job's timeline

- Command: a loop of `zoom push t,t+1` then `thread markers --search name:Memory --list --limit 1 --json`, reading `data.used`, on the resource-usage profile.
- Expected: one command that gives a time series of the `Memory` markers' `used` value, like `counter info` does for counters.
- Got: `counter list` says "No counters in this profile". The values are only available as a marker payload field.
- Would have helped: show the resource-usage profile's CPU and Memory markers as counters, or add `thread markers --search name:Memory --field used --buckets 20`.
