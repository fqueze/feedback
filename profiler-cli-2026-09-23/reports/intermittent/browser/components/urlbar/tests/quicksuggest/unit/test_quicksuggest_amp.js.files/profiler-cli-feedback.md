## Question: how busy was the whole machine during one test's window (resource-usage profile)?

- Command: `profiler-cli zoom push <test marker>` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0`
- Expected: some summary of the `CPU Percent` field over the zoomed range (mean/median/min, or a per-bucket series), since `counter list` says "No counters in this profile" and the CPU data is only in `CPU Use` markers.
- Got: one row per 100 ms marker with no payload values at all (just name/time/duration), so 314 rows that say nothing about CPU.
- Workaround: `--json` and a Python script averaging `data.cpuPercent` over the markers.
- What would have answered it: `thread markers --search "name:CPU Use"` (aggregated, not --list) showing field stats (min/median/max) for numeric payload fields, or the resource-usage CPU exposed as a counter so `counter info` works under zoom.

## Question: what share of one core did the process get over a time range? (review-test_quicksuggest_amp.js)

- Profile: FqGzsI9kSLasNquTL6_NRg `profile_test_quicksuggest_amp.js.json` (mac), counter `c-1` Process CPU.
- Command: `profiler-cli counter info c-1` at full range, then after `zoom push 9.162,12.216`, and `thread info` in the same zoom.
- Expected: the same period reads the same percentage whatever the zoom, and matches `thread info`'s CPU for the main thread (the process has almost no other CPU).
- Got: the full-range bucket 9.162-12.216s reads 19%; zoomed to exactly that range, its 10 sub-buckets read 19-68% (about 41% on average); `thread info` says 24% (713.9 ms in 3.0 s). Zoomed to 4-30.13s the buckets read 30-39% against `thread info`'s 25%. Linux profile FN28SmhaRQOc78Qf-Okjrw did agree (16-29% vs 19%). The original report quoted the 30-39%.
- Workaround: trust `thread info`'s "N% for X ms" over the counter buckets.
