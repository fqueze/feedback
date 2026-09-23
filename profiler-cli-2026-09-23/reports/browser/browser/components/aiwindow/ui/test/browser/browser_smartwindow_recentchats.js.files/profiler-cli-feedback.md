## Bare `-term` exclusion in `thread markers --search` is silently ignored
- Command: `profiler-cli thread markers --session <s> --search "browser_smartwindow_recentchats.js,-parseXULToFragment,-customElements.js" --list --limit 0`
- Expected: the 796 markers minus the ~500 `NS_ERROR_FAILURE ... customElements.js:529` console errors, or an error saying exclusions need `field:value`.
- Got: the same 796 markers, no warning.
- Workaround: grep the text output.

## `--list` rows for Console-message markers carry their whole multi-line JS stack
- Command: `profiler-cli thread markers --session <s> --search "name:INFO,name:PASS,name:FAIL,name:test,name:output" --list --limit 0` zoomed on one 4 s test
- Expected: one line per marker (the stack is available via `marker stack`).
- Got: 3553 lines for ~800 markers, because each `Console message: [JavaScript Error ...]` embeds a 15-line stack; it breaks line-oriented filtering.
- Workaround: `grep -E "^\s+m-[0-9]+"` on the output.

## Question: "was the machine busy or idle during this 6-minute hang?" (resource-usage profile)
- `profiler-cli counter list` says "No counters in this profile", although the resource-usage profile has `CPU Use` markers every 100 ms with `cpuPercent`.
- Command used: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` in a zoom, then a Python script bucketing `data.cpuPercent` per 30 s.
- What would have answered it: an aggregate (min/avg/max, or a sparkline over the zoom) of numeric marker fields, or exposing the CPU Use markers as a counter.

## Question: "which handle is the marker a profiler URL's `marker=N` points to?" (review of this report)
- Command: `profiler-cli marker info --session <s> m-341..m-558 --json`, then a Python filter on `markerIndex == 41186`; ranges over 256 handles fail ("covers 325 handles, more than the maximum of 256"), so it took two calls per profile.
- Expected: a way to address a marker by its index, e.g. `marker info --index 41186`, or `load <url>` printing the handle of the URL's `marker=N`.
