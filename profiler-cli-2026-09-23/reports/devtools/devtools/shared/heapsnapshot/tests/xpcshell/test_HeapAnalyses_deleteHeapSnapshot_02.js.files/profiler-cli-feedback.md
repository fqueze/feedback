## Question: "how busy was the machine, per 5 s, over this range?"

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0 --json`, then a Python script averaging the `cpuPercent` field (a string like `"17.9%"`) per 5 s bucket.
- The default output of `thread markers --search "name:CPU Use"` in a zoom gives only the marker count and interval stats, not the values. A resource-usage profile has no counters (`counter list`: "No counters in this profile."), so `counter info` cannot answer it either.
- What could have shown it: a per-bucket summary of a numeric payload field, e.g. `thread markers --search "name:CPU Use" --summarize-field cpuPercent`, or exposing the CPU Use markers as a counter.

## Question: "how many tests started in this range, and with which status?"

- Command: `thread markers --search "status:TIMEOUT" --list --limit 0 --json` plus a script filtering on `start`.
- Zooming does not answer it: the zoom keeps markers that overlap the range, so a long `test` marker that started earlier (m-26, 1m31s) is counted too.
- What could have shown it: a `--starts-in <range>` filter, or a start-time histogram in the aggregate view.

## Question (review): "which tests started but never ended?"

- Command: `thread markers --search "will retry" --list --limit 0 --json` and `thread markers --search "name:test" --list --limit 0 --json`, then a script joining the test names.
- A resource-usage profile makes a `test` marker only at `test_end`. A test whose run raised before any `test_end` leaves no marker. Its `test_start` is invisible, and so is its absence. That was 312 tests in this job.
- What could have shown it: an open-ended `test` marker (status "none", to the end of the profile) for a `test_start` without a `test_end`. This could be done in the resource monitor, or in profiler-cli as a synthesized marker.
