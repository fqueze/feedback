## Question: which document does the innerWindowID of a marker belong to?

- Command: `profiler-cli thread markers --search '4294967338' --session <s>` and `--search 'innerWindowID:4294967338'`
- Expected: the markers of that window (DocumentLoad with its URL, RefreshDriverTick, ...), or a command mapping an innerWindowID to its page URL.
- Got: 0 markers for both, although `--group-by field:innerWindowID` groups by that same field, and `marker info` shows `innerWindowID: 4294967338` under "Other payload fields (no schema)".
- Workaround: list DocumentLoad and nsRefreshDriver markers of the process and `marker info` them one by one until one shows the matching innerWindowID.
