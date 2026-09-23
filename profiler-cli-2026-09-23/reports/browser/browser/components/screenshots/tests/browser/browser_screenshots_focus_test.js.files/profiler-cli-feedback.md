## Default selected thread after `load` is not the parent main thread

- Command: `profiler-cli load "<.../profile_browser_screenshots_focus_test-2.js.json>" --session browser-focus-2` then `profiler-cli thread markers --session browser-focus-2 --category Test --search browser_screenshots_focus_test --list`
- Expected: the parent process GeckoMain selected by default (as it was for the first profile of the same job), so the test log comes back.
- Got: `t-21 (GeckoMain, WebExtensions)` was selected, and the query printed "No markers match the specified filters." — which reads like the test left no log.
- Workaround: `profile info --search "Parent Process"`, then `thread select t-0`.

## Question: which marker a link's `marker=N` points to (review)

- Command: `profiler-cli marker info m-258 m-327 m-340 m-345 --session <s>` to check the `marker=549737` etc. of a report's links.
- Expected: the marker index next to the handle, e.g. `Marker m-258 (index 549737)`, so checking a link is one plain call; or `marker info --index 549737` to go from the link to the marker directly.
- Got: the text output has no index; only `--json` has `markerIndex`, so checking 20 links took a Python filter over the JSON.
- Workaround: `marker info <handles…> --json | python3 -c '…print(handle, markerIndex, name, start)'`.
