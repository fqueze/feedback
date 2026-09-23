## Machine CPU during one test, in a resource-usage profile

- Question: "what was the whole machine's CPU use while this test ran?" (to tell a slow test from a saturated machine).
- Command: `profiler-cli zoom push m-1 --session twk-ru1; profiler-cli thread markers --session twk-ru1 --search 'name:CPU Use' --list --limit 0 --json`, then a Python script averaging `data.cpuPercent` (a string like `"100.0%"`).
- Expected: `profile info` or `thread markers --search 'name:CPU Use'` (aggregate view) to print a mean/median/max of the CPU Percent field over the zoomed range. `profile info` printed "CPU activity over time: No significant activity." and `counter list` "No counters in this profile.", because the machine CPU is carried as `CPU Use` interval markers rather than a counter.
- Got: only per-marker values; the aggregate shows marker counts and durations, not field statistics.
- Workaround: script over `--json`.
- What would have answered it: numeric field stats (min/mean/max) in the aggregate marker view for a numeric payload field, or `profile info` reading `CPU Use` markers as the machine CPU track.

## `thread samples` after `zoom clear` reports the last zoomed range (review-test_weather_keywords.js, profiler-cli 0.9.0)

- Command: on the Linux per-test profile (`profile_test_weather_keywords.js.json` of task MKiOFl-6RcaqTI4Wr9y5ow, 2787 main-thread samples), `profiler-cli zoom push 29,30.266 --session S; profiler-cli thread samples --session S --include-idle; profiler-cli zoom clear --session S; profiler-cli thread samples --session S --include-idle`.
- Expected: the second `thread samples` to cover the full profile, 2787 samples.
- Got: `[... | View: Full profile | ...]` in the header and `status` saying "View range: Full profile", but `Categories (124 running samples)`, the count of the 29-30.266 s range. It stayed stale across several calls (with and without `--include-idle`, and with `--json`) until another `zoom push`; `zoom pop` behaves the same.
- Cost: I first read the last 3 s as the whole profile, and nearly reported wrong idle shares.
- Workaround: `zoom push 0,<end>` instead of `zoom clear` before a full-profile query.
