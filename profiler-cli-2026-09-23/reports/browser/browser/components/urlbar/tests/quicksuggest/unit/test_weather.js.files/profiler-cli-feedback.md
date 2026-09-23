## Question: "what was the machine's CPU use while this test ran?" (resource-usage profile)

- Command: `profiler-cli zoom push <test marker range>` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json`, then a Python script to average `data.cpuPercent` / `idle_pct` over the 319 markers.
- Expected: the default (non-JSON) `thread markers --search "name:CPU Use"` summary to give the mean/median of the CPU percentage in the view, or `--list` rows to show the `cpuPercent` field.
- Got: the list rows show only name, time and duration (`m-2405 CPU Use t=4m29s 106ms`), no value; the aggregate shows marker counts and interval stats only. Also `profile info` says "CPU activity over time: No significant activity" and `counter list` says "No counters in this profile" on a resource-usage profile whose CPU data is all in `CPU Use` markers, which reads as "the machine was idle".
- Workaround: `--json` and a script.

## `marker info m-A..m-B` over a filtered list's first and last rows is not that list (reviewer, browser-review-test_weather.js)

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0` inside `zoom push <test marker>` (319 rows, first `m-7757`, last `m-8071`), then `profiler-cli marker info m-7757..m-8071 --json`.
- Expected: the 319 listed markers, as the guide's "Inclusive range of handles, e.g. consecutive list rows" suggests.
- Got: "covers 315 handles, more than the maximum of 256". The list is in time order but its handles are not contiguous, so the range spans 315 handles for 319 rows: it would have both missed rows and included other markers. Only the 256 cap made this visible.
- Workaround: pass the handles individually, in two batches of at most 256.
