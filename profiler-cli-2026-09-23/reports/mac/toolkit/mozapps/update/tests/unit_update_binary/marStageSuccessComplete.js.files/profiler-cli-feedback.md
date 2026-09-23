## Which tests were running during a 2.5 s window of a 26-minute resource-usage profile

- Command: `profiler-cli thread markers --search "unit_update_binary" --list --limit 0 --session <s>` on a resource-usage profile (BN1M-VxuRRuIsgCewqS8Bg).
- Expected: each `test` marker's start (and end) precise enough to tell which tests overlapped a 2.5 s updater run.
- Got: `t=16m51s` for 13 different tests: past one minute, `--list` rounds starts to whole seconds and shows only the duration, so overlap within a second cannot be read. `marker info` also prints `16m51s - 17m25s`.
- Workaround: `--list --json` and a script printing `start/1000` with the label.
- What would have answered it: millisecond starts (e.g. `t=1010.701s`) in `--list` when the profile is long, or an `--end` column.

## Machine CPU over a time range, from `CPU Use` markers

- Command: `profiler-cli thread markers --search "CPU Use" --list --limit 0` after `zoom push 1005,1047`.
- Expected: the CPU percentage per marker (or per second).
- Got: rows with only name, time and duration; the `CPU Percent` field is only in `marker info` / `--json`. `counter list` says the profile has no counters.
- Workaround: `--json`, then average `data.cpuPercent` per second in a script.
- What would have answered it: show the marker's main field in `--list` rows for `CPU` markers, or a `profile cpu --range` summary.
