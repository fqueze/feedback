# profiler-cli feedback (test_assert.js)

## Question: "was the machine saturated while this test ran?"
- Command: `profiler-cli thread markers --session test_assert.js-1 --search "name:CPU Use" --list --limit 0` (resource-usage profile, xpcshell job)
- Expected: some way to get the machine CPU over a zoomed range (average/max of `cpuPercent`), or the value in the list rows.
- Got: 14k rows of `CPU Use  t=1m6s  93ms` with no value; `counter list` says "No counters in this profile". The values are only in `--json` `data.cpuPercent`.
- Workaround: script over `--json` to average `cpuPercent` for markers in [start, end) of the test marker. A `zoom push <test marker>` + summary of CPU markers (mean/max CPU%) would answer it directly.

## Question: "how long after the test started did X happen?"
- Command: `profiler-cli thread markers --search test_assert.js --list --limit 0`
- Expected: timestamps precise enough to tell that `will retry` came 14 ms after the test marker started.
- Got: both rows show `t=1m6s` (rounded to the second past 1 min); `marker info` also shows `1m6s - 1m51s`.
- Workaround: `--json` and read `start`. Milliseconds in the list (at least when two rows share the same rounded time) would avoid it.

## Question: "which processes of package X existed at each moment?" (Android harness `get_process_list` DEBUG lines)
- Command: `profiler-cli thread markers --session test_assert.js-4 --search get_process_list --list --limit 0`
- Got: each row's label is the start of a very long Python list, cut long before the interesting entries.
- Workaround: `--json` + regex on the label. Not a tool bug as such; noting the question in case a "grep inside label" mode is cheap (`--search` matched, but the matched part is not shown).
