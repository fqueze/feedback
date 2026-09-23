## Marker times in `--list` and `marker info` are rounded to the second past 1 minute

- Question: how many ms between the test's `INFO Close permission prompt` and the `Hit MOZ_CRASH` output line, per job.
- Command: `profiler-cli thread markers --session <s> --search camera_preview --list --limit 0`, then `profiler-cli marker info m-76..m-83`
- Expected: millisecond start times, e.g. `t=268.795s`.
- Got: `t=4m28s` / `t=4m29s` for every row, and `Time: 4m29s (instant)` in `marker info`. The 18 ms gap that is the whole finding cannot be seen.
- Workaround: `--json`, then a Python script reading `flatMarkers[].start`. Keeping ms precision (e.g. `4m28.795s`) in the default output would have answered it.
