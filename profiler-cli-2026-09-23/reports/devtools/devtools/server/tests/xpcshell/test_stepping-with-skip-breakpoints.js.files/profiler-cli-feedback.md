## Question: when exactly did the test start, and how long after that did the harness log "will retry"? (test_stepping-with-skip-breakpoints.js, 2026-09-22)

- Command: `profiler-cli thread markers --search test_stepping-with-skip-breakpoints --list --limit 0 --session <s>` on a resource-usage profile (LKys8j8iTQKTAN9hKv040g).
- Expected: start times precise enough to tell a 39 ms gap from a 1 s one; the whole diagnosis rests on "will retry" coming tens of ms after the test's start.
- Got: `t=2m14s` for both markers, at one-second resolution.
- Workaround: a Python script over `--json` reading `flatMarkers[].start` (133541.909 and 133580.908). The list could print milliseconds, at least when two markers in the listing fall in the same second.

## Question: what was whole-machine CPU during this test's 45 s? (same report)

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --session <s>` after `zoom push 133.5,178.6`.
- Expected: each marker's CPU percentage, or a min/avg/max over the zoom.
- Got: rows with an empty label (`m-513  CPU Use  t=2m13s  110ms  ✗`), so no value. A script over `--json` failed too, because the value is only in `marker info`'s `fields`, not in the flat list's label.
- Workaround: `thread markers --search "name:CPU Use" --group-by field:cpuPercent` gave a histogram (321 of 441 at 0.0%). That answers it, but a `cpuPercent` summary over the zoom (min/avg/max) would answer it directly.

## Question: how many `CPU Use` samples in a zoom are above 20%? (review of this report, 2026-09-22)

- Command: `profiler-cli thread markers --search "name:CPU Use" --group-by field:cpuPercent --session <s>` after `zoom push 133.5,178.6` (LKys8j8i, profiler-cli 0.9.0).
- Expected: an answer to a threshold question on a numeric field.
- Got: one group per distinct value, sorted by count. The values above 20% each have a count of 1 and are scattered through a list of about 40 groups. The report author read "above 20% only once (80%)" and "321 of 441" from this list. The actual figures are 14 above 20% and 325 of 452.
- Workaround: a script over `thread markers --list --limit 0 --json`, reading `flatMarkers[].data.cpuPercent`. That field is present in 0.9.0, which the earlier entry said it was not. Instant markers have no `duration` key, so a script that reads it without `.get` raises KeyError. A numeric summary (min/avg/max, or `--min-value`) for a field under zoom would answer this directly.
