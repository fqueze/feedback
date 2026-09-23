## Question: "how many tests of each status started in each time slice?" (needed a script over `thread markers --list --json`)
- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json`, then Python bucketing by `start` and `data.status`.
- Could have shown: `--group-by field:status` combined with a time-bucket option (e.g. `--bucket 5s`). In the resource-usage profile that says at a glance "every test after t=50 s is TIMEOUT".

## Question: "which tests were in flight at time T?" (needed a script over the same JSON)
- Could have shown: an `--overlapping <t>` filter for interval markers, i.e. markers whose [start, end] contains t. `zoom push t,t` plus a list might do it, but it is not documented as meaning "overlapping".
