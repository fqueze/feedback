## Question: "in which order did these markers happen, to the millisecond?" (profiler-cli, thread markers --list)

- Command: `profiler-cli thread markers --session <s> --category Test --search test_builtin_version_upgrades --list --limit 0` on a 1m25s profile.
- Expected: marker start times precise enough to order events a few ms apart (e.g. `t=75.428s`).
- Got: `t=1m15s` for every marker between 75.0 and 75.9 s, and `marker info m-N` also prints `Time: 1m15s`. Past one minute the time is rounded to the second, so a reread at 75.747 s and an assertion at 75.428 s look simultaneous.
- Workaround: `--json` on both commands and a script reading `.flatMarkers[].start` / `.start`, divided by 1000.
- What the default output could show: seconds with millisecond precision (`75.428s`, or `1m15.428s`), as it already does below one minute (`33.052s`).

## Question: "which marker is the one this link's `marker=N` points to?" (profiler-cli 0.9.0, review of this report)

- Command: `profiler-cli thread markers --session <s> --search "test_builtin_version_upgrades" --list --limit 0`, then `profiler-cli marker info <m-…> --session <s> --json` per row to read `markerIndex`.
- Expected: a way to go from a link's `marker=N` to the marker, or the marker index next to each `--list` row, since profiler links carry indexes and the CLI shows handles.
- Got: handles only; the index is in `marker info --json` alone. Checking about 40 links took a script calling `--list --json` and then `marker info --json` on every handle.
- Workaround: a small script (`review/find.py` in this directory).
- What the output could show: an index column in `--list` (or `marker info --index N`).
