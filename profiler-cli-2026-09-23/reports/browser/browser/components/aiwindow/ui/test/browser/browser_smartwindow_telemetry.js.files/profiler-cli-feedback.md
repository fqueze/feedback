## Question: "in what order did these markers happen, to the millisecond?"
- Command: `profiler-cli thread markers --search "Event::record,TEST-,TestUtils" --list --limit 0 --session S`
- The list prints `t=11m5s` for every row, so a test failure, a Glean `Event::record` and a `TestUtils waitForCondition` 0.6 ms apart all read the same time. I had to script over `--json` (`flatMarkers[].start`) to get the order that is the whole diagnosis.
- Could have shown: millisecond start times (e.g. `t=664535.316ms` or `11m4.535s`) in `--list`, at least when zoomed or when rows share the same rounded time.

## `zoom push` with an out-of-range value gives an empty view without a warning
- Command: `profiler-cli zoom push 664299,664700 --session S` (I meant ms; the profile is 11m10s long)
- Expected: an error that the range is outside the profile, or a hint that values are seconds.
- Got: `View: ts>10X→ts>10y (6m41s)` and then "No markers in this thread", which reads like a real empty range.
- Workaround: `zoom push 664.299,664.700`, or `zoom push m-<task marker>`.

## Review: `load` reports a timeout as a failure while the daemon keeps loading
- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/E1o61fg7TcSqLQeHs8Qp1g/runs/0/artifacts/public/test_info/profile_browser_smartwindow_telemetry.js.json' --session browser-review-browser_smartwindow_telemetry.js-4`
- Expected: either wait until loaded, or say the load continues in the background and how to wait for it.
- Got: `Error: Profile load timeout after 60000ms`, exit 1, then "Profile still loading" on the next commands; the session did become usable a little later.
- Workaround: poll `profiler-cli status --session S` until it succeeds.
- Also, again in review: `--list` prints `t=11m5s` for rows milliseconds apart, so I scripted over `--json` for `start` (same question as logged above).
