## Question: which document a "RefreshDriverTick waiting for paint" marker belongs to

- Command: `profiler-cli thread markers --search "innerWindowID:4294967338" --session …`
- Expected: the markers for that window. `innerWindowID` is shown under `rawFields` by `marker info --json`, and `--group-by field:innerWindowID` works.
- Got: `No markers match the specified filters.`
- Workaround: `--group-by field:innerWindowID`, then compare the IDs with `marker info --json` on the `nsRefreshDriver initial timer start …/popup.html` marker. It would be even better if the marker list printed the document URL for innerWindowID-bearing markers.

## Question: the exact time of a marker after 1 minute

- Command: `profiler-cli thread markers --category Test --search … --list`
- Expected: millisecond times, which I need to zoom between two log lines.
- Got: `t=1m40s` and `t=1m49s`, so I needed `marker info m-795 m-16 --json` to read `start`. `zoom push m-795,m-16` (the span between two markers) is also rejected: `Invalid time value: "m-795"`.
- Workaround: `marker info --json`, then `zoom push 100.3,108.7`.
