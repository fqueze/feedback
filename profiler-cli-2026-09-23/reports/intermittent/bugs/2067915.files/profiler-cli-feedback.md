
## review-2067915: machine CPU over a test's range (resource-usage profile)

- Question: how busy was the whole machine while one test ran, per 25 s bucket, and how does that compare with the rest of the job?
- Command: `profiler-cli zoom push m-2` (the test's `test` marker) then `profiler-cli thread markers --search "CPU Use" --list --limit 0 --json`, and a Python script over `flatMarkers[].fields[cpuPercent]`.
- Expected: something like `counter info`'s bucketed summary. In the resource-usage profile the machine CPU is `CPU Use` markers, not a counter, so `counter list` says "No counters" and `profile info` says "No significant activity".
- Could have shown: the `counter info`-style buckets (mean, and share of samples above 90%) for `CPU Use` markers in the current zoom.

## review-2067915: load of a raw Taskcluster URL timed out, then kept loading

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/.../profile_browser_aiwindow_group_tabs_button.js.json --session review-2067915-3`
- Got: `Error: Profile load timeout after 60000ms`, but the daemon went on loading and `status` answered "Profile still loading" until it was ready. The error reads as a failure. Once loaded, the selected thread was t-170 (Privileged Content), not the parent GeckoMain that loading the profiler.firefox.com link of the same profile selected.
- Workaround: poll `status` until it shows a selected thread, then `thread select t-0`.
