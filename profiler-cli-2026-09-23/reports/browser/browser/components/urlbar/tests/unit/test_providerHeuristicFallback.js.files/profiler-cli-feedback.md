## Question: what was the machine's CPU use while one test ran (resource-usage profile)?

- Command: `profiler-cli zoom push m-1` (the `test` marker) then `profiler-cli thread markers --search 'name:CPU Use' --list --limit 0 --json`, and a Python script averaging `data.cpuPercent` over the 392 markers.
- Expected: `counter list` / `counter info` to show machine CPU for the zoomed range, as `profile info` suggests counters are where CPU lives.
- Got: `No counters in this profile.`; the CPU data is only in 18,390 `CPU Use` interval markers, whose value is only visible one at a time via `marker info`.
- What the output could have shown: a min/mean/max of `CPU Percent` for the `CPU Use` markers in the current zoom (e.g. in the `thread markers` aggregate for a marker type with a numeric field), or a synthesized counter from those markers.

## Question: the per-query durations of a test (gaps between successive log markers)

- Command: `thread markers --search 'Creating new queryContext' --list --limit 0`, then a script to diff timestamps.
- A search string containing U+2028 splits the listed line in two, so line-based parsing of `--list` breaks (the `t=` field lands on another line).
- What the output could have shown: an option to print the gap to the previous listed marker.

## (review) `thread samples` after `zoom clear` reports the previous zoom's samples

- Command: `zoom push 30,30.549`, `thread samples --include-idle`, `zoom clear`, `thread samples --include-idle` (session loaded from the Al6uY0QFQM per-test profile).
- Expected: the second call to cover the full profile (557 GeckoMain samples); `status` and the header both say `View: Full profile`.
- Got: `Categories (9 running samples)`, the same as the zoomed call. After a zoom on 3.887-4.222 it said 7 for the "full profile" too. Pushing a new zoom (10,20) gives the right count (184).
- Workaround: read the raw profile's `samples` with a script, or push an explicit full-range zoom.

## (review) "N running samples" counts idle samples too, and never says how many samples the range should have

- Command: `zoom push 3.887,4.222`, `thread samples --include-idle` on t-0 and on t-1.
- Got: both say `7 running samples`; for the DOM Worker all 7 are `Idle`, for GeckoMain none are. The original report read "7 running samples in 335 ms" as "mostly idle", when the sampler had only taken 7 samples there at all (median 56 ms apart, for a configured 10 ms).
- What the output could have shown: "7 samples (0 idle), median spacing 56 ms (configured 10 ms)".

## (review) profile-link.py refuses a session loaded from a profiler.firefox.com link

- Command: `profile-link.py --session <s> --thread t-0 --range 3.887,4.222 --panel stack-chart`, the session loaded from the report's `from-url` link, as review-brief.md says to do.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself`. It could extract the artifact URL from the `from-url/` link it was given. Workaround: reload from the Taskcluster URL.
