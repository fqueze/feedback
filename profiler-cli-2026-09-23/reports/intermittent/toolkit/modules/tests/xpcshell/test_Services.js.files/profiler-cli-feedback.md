## Question: what was the machine's CPU use while one test ran (resource-usage profile)

- Command: `profiler-cli thread markers --session test_Services.js-4 --search "name:CPU Use" --list --limit 0` after `zoom push 361.5,363`
- Expected: each `CPU Use` row showing its cpuPercent / idle_pct, the way other marker rows show their payload text.
- Got: rows with only name, start time and duration (`m-199 CPU Use t=6m1s 63ms`), no value.
- Workaround: `marker info m-194..m-209 --json` piped to a Python script to print cpuPercent/idle_pct per row.
- What the output could have shown: the payload fields inline in `--list`, or a `thread markers --search "CPU Use" --stats cpuPercent`-style summary over the zoom.

## zoom push takes seconds while the marker list prints milliseconds

- Command: `profiler-cli zoom push 152.5,163 --session test_Services.js-1` on a 171 ms profile whose `--list` printed `t=152.54ms`.
- Expected: either the unit accepted from the printed times (`152.5ms,163ms`), or an error since the range is far outside the 171 ms profile.
- Got: a silent zoom to a 10.5 s range with no markers ("0 markers in view"), i.e. an empty answer that looks like "nothing happened there".
- Workaround: `zoom push 0.1525,0.163`.
