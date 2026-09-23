## `thread markers --list` rounds times past one minute to the second

- Command: `profiler-cli thread markers --session test_nesting-04.js-1 --search "name:C++ warning" --list`
- Expected: millisecond timestamps, as shown under one minute (`t=45.706s`).
- Got: `t=2m12s` for every row, so I could not order the first process-launch failure against the harness's "will retry" lines or the test starts, which were all in the same second.
- Workaround: `--json` and read `start`, or `zoom push` down to a sub-second range.

## Question: "which tests were running at time t" (for the resource-usage profile)

- I needed the set of `test` markers overlapping one instant (the onset of a job-wide breakdown), in five jobs, to intersect them.
- Command used: `profiler-cli thread markers --search name:test --list --limit 0 --json`, then a Python script to filter start <= t <= end and to histogram test starts by status.
- What could have answered it: an `--at <t>` (or `--overlapping <t>`) filter on `thread markers`, returning the interval markers that contain t.
