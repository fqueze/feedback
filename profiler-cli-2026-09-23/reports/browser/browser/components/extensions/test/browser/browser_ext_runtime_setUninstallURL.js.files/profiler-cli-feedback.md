## `thread markers --search` does not match schema-less payload fields (innerWindowID)

- Command: `profiler-cli thread markers --session browser-setUninstallURL-1 --search "innerWindowID:4294967338,-name:RefreshDriverTick waiting for paint" --list` and `--search 4294967338` on the WebExtensions GeckoMain thread.
- Expected: the markers (DocumentLoad, nsRefreshDriver, ...) whose innerWindowID is 4294967338, i.e. which document the 13,004 "RefreshDriverTick waiting for paint" markers belong to.
- Got: "No markers match the specified filters." The value is only in `rawFields` ("Fields (raw payload, no schema)"), which search ignores, although `--group-by field:innerWindowID` does read it.
- Workaround: zoom to the time the ticks started, list DocumentLoad/nsRefreshDriver markers there, and `marker info --json` each to compare rawFields.innerWindowID. Better: let search match raw fields, or resolve innerWindowID to the page URL in marker output (the profile's pages table has it).
