## Question: the exact time of each marker in a `--list`, to order markers within one second
- Command: `profiler-cli thread markers --session <s> --list --limit 0` (zoomed on 711–721 s of a 20 min resource-usage profile)
- Expected: start times precise enough to order a slot's `Application ran for`, the next test's `test` marker start and a `Failed to start process` a few ms apart.
- Got: `t=11m57s` — past one minute, times are rounded to whole seconds, so ten markers in the same second are indistinguishable.
- Workaround: `--json` and a Python script over `flatMarkers[].start`. Printing `t=717.324s` (or ms after the minute) in the list would have answered it.

## `thread markers --search` with only exclusion terms matches nothing
- Command: `profiler-cli thread markers --session <s> --search "-name:test,-will retry,-name:CPU Use" --list` (inside a zoom)
- Expected: every marker in the zoom except the excluded ones.
- Got: `No markers match the specified filters.`
- Workaround: add a positive term (`name:INFO,-will retry`). If exclusion-only is intentionally empty, saying so in the output would avoid a false "nothing here".

## Question (review): which marker is `marker=N` in a link, when it is one of 1,149 identical markers
- Command: `profiler-cli thread markers --session <s> --search "not killing" --list --limit 0`, then `marker info <m-…> --json` to find `markerIndex` 10066.
- Expected: a way to go from a link's `marker=N` to its handle, e.g. `marker info --index 10066`, or the list showing each row's index.
- Got: handles only; finding index 10066 among 1,149 `not killing -- proc or pid unknown` markers needed `marker info` on 3,620 handles and a script over the JSON.
- Also hit the entry above (times rounded to whole seconds past 1 min) again, for slot ordering on Android.
