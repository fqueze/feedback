## Question: how busy was the machine during this test? (resource-usage profile)
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <s>` (zoomed to the test's `test` marker)
- Expected: each CPU Use row showing its CPU percent (the marker's main field), or a sparkline/summary of CPU % over the range.
- Got: 67 rows with only name, time and duration (`m-290 CPU Use t=2m39s 94ms`); the percentage appears only in `marker info` or `--json`.
- Workaround: `--json` and a Python script over `flatMarkers[].fields` to print cpuPercent per 100 ms.
- What could have shown it: put the CPU Percent field in the list row, or a `counter`-style "over time" summary for CPU Use markers.

## Question: which threads were busy in this time window?
- Command: `profiler-cli zoom push 16.46,20.40` then `profiler-cli profile info --session <s>`
- Expected: per-thread CPU restricted to the zoomed range.
- Got: the whole-profile CPU totals (same numbers as unzoomed), only the header showed the zoom.
- Workaround: `thread info` / `counter info c-N` per thread, one at a time.

## Question (review): which markers started on this thread between 15.90s and 15.95s?
- Command: `profiler-cli zoom push 15.90,15.95` then `profiler-cli thread markers --list --limit 0 --session <s>`
- Expected: the markers that start in the window, the way the marker table reads.
- Got: 6,426 rows. They begin with dozens of IPC markers that started at 2.4s and 15.4s and last 5–14 s, followed by about 6,000 bulk `Preference Read ... User` rows from the harness's pref snapshot.
- Workaround: `--json`, then a script that keeps markers starting at or after 15.90s and drops the IPC rows and the `prefKind: User` reads.
- What could have shown it: a `--starting-in-range` option, or listing markers that started before the zoom separately. Also a way to exclude by field value (`-prefKind:User` did not come to mind as a way to drop them).

## Question (review): where are the session_restore.collect_data timings?
- Command: `profiler-cli thread markers --search "session_restore.collect_data,PrivacyFilter.sys.mjs" --list --session <s>`
- Expected: the `TimingDistribution::start ... session_restore.collect_data` rows, whose list text shows exactly that string.
- Got: only the PrivacyFilter rows. `--search collect_data` finds them.
- Workaround: search for a substring of a single payload field.
- What could have shown it: have `--search` also match the row's displayed description, or say in `--help` that the dotted name is built from two fields.
