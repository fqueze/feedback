## Question: "over what time span does one window's refresh driver keep ticking?"

- Command: `profiler-cli thread markers --search "name:RefreshDriverTick waiting for paint" --group-by field:innerWindowID --session browser-webRequest_error_after-1`
- Expected: per group, the count plus the first and last start time (and possibly the largest gap).
- Got: the count per innerWindowID (26,133 for the leaked popup), and three example handles, but no time span.
- Workaround: `--list --limit 0 --json` piped into a Python script to filter by `data.innerWindowID` and print the first and last `start`.
- What would have answered it: first/last time (the group's span) in the `--group-by` output.
