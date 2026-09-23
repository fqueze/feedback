## Marker times past one minute are printed at 1 s resolution

- Command: `profiler-cli thread markers --session <s> --search corrupt_nobackup_default --list --limit 0` and `profiler-cli marker info m-182 m-59 --session <s>`
- Expected: millisecond timestamps (e.g. `t=62.927s`), since the question was the order and spacing of events a few hundred ms to a few seconds apart (test exit, "may be hanging at shutdown", GPU child's late `Reply_Init`).
- Got: `t=1m3s`, `t=1m10s`, `Time: 1m14s (instant)` — every marker in the same second prints the same time.
- Workaround: `--json` and a Python one-liner printing `start`.

## Question: "what was the machine's CPU use over this time range" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0 --json | python3 ...` bucketing `data.cpuPercent` per 2 s.
- The resource-usage profile has no counters (`counter list` is empty); CPU use lives only in `CPU Use` markers. Nothing summarises them over a zoom range: the aggregate view gives only their durations.
- What would have answered it: a min/avg/max of `CPU Percent` per marker type in the zoomed range (or `CPU Use` exposed as a counter so `counter info` works after `zoom push`).
