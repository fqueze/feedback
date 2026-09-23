## Question: which marker does a link's `marker=N` select? (review)

- Command: `profiler-cli thread markers --session S --search waitForCondition --list --limit 0 --json`, to find the marker behind a report link's `marker=804801`.
- Expected: a way to go from a marker index to its handle, e.g. `markerIndex` in `flatMarkers[]`, or `marker info --index 804801`.
- Got: `flatMarkers[]` has no `markerIndex`; only `marker info m-…` reports it. The link turned out to select the vsync wait, not the `checking for open popups` INFO the report quoted.
- Workaround: `marker info m-A..m-B --json` over candidate handles, and compare `markerIndex`.
