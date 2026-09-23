## Question: "at what exact time did this marker fire, so I can zoom around it?"
- Command: `profiler-cli thread markers --session S --category Test --search <test> --list --limit 0`
- Expected: timestamps precise enough to `zoom push` a sub-second window around a log line (e.g. t=195.170s).
- Got: `t=3m15s` for every row after the first minutes; several markers 3 ms apart all read `t=3m15s`.
- Workaround: `--json` and read `.flatMarkers[].start` (ms) with a script.
- Could show: seconds with millisecond precision (as `zoom push` accepts), at least when rows share a rounded value.

## Question: "was the machine CPU-saturated around time T?" (resource-usage profile)
- Command: `profiler-cli profile info --session S2` on `profile_resource-usage.json`
- Expected: the machine CPU track summarized over time.
- Got: "CPU activity over time: No significant activity." and `counter list` says "No counters in this profile", although the profile has 20,137 `CPU Use` markers with a `cpuPercent` field.
- Workaround: `thread markers --search "name:CPU Use" --list --json` and a script averaging `cpuPercent` per second.
- Could show: treat `CPU Use` markers as the CPU track in `profile info`/`counter info`, or a per-bucket summary of a numeric marker field.

## `--search name:test` is a substring match
- Command: `thread markers --category Test --search "name:test,TEST-UNEXPECTED-FAIL" --list`
- Expected: the `test` markers (one per test) and the failures.
- Got: 415 rows, every `TEST-PASS` etc. included since `name:test` matches substrings case-insensitively.
- Workaround: post-filter the output with `rg ' test  '`.
- Could show: an exact-match form (e.g. `name:=test`).
