## Question: "what was the machine's CPU use over this test's time range?" (resource-usage profile)

- Command: `profiler-cli zoom push m-1 --session <s>` (the `test` marker), then `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- Expected: a summary of the `cpuPercent` field over the zoomed range (mean/max, or a sparkline), since `counter list` reports "No counters in this profile" for resource-usage profiles.
- Got: 103 rows of `CPU Use` with only their durations; the percentage is only visible per marker via `marker info`.
- Workaround: `--json` and a Python script averaging `fields[key=cpuPercent]`.
- What would have answered it: `thread markers --search "name:CPU Use"` in aggregate mode showing field stats (min/mean/max of numeric payload fields), or exposing CPU Use as a counter.

## Question: "which ICE pair lines in the job's `output` markers say IN_PROGRESS / SUCCEEDED?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:output" --list --limit 0 | rg -i "IN_PROGRESS|SUCCEEDED|grace period is over"`
- Expected: rows whose visible text contains the matched term.
- Got: the row label is the head of the payload, cut at the terminal width; the part that matches (the end of a long nICEr line) is not shown, so matches looked unrelated (e.g. `(registry/INFO) insert ...` rows matched "failed" elsewhere in the payload), and I had to call `marker info` on each handle to see why it matched.
- Workaround: `marker info m-a m-b ... --json` and print the tail of each field.
- What would have answered it: with `--search`, show a snippet of the payload around the match rather than its head.
