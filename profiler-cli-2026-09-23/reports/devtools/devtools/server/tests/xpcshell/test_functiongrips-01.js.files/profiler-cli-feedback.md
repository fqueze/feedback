# profiler-cli feedback — test_functiongrips-01.js

## Question: "did this test start before or after the first launch failure, and how long did it run before giving up?"

- Command: `profiler-cli marker info m-1..m-6 --session test_functiongrips-01.js-1` (and `thread markers --search functiongrips-01 --list`)
- Expected: start times precise enough to compare markers a few ms apart (test start at 133.184s, `will retry` at 133.222s, first `Failed to launch` at 131.676s).
- Got: `Time: 2m13s - 2m58s (45.038s)` and `t=2m13s` for both the test start and its `will retry` line; the 38 ms gap and the 1.5 s gap to the launch failure are invisible.
- Workaround: `--json` and reading `start` from `flatMarkers`.
- Could show: millisecond start times in `marker info` (and in `--list` when the profile is short, or when rows share the same second).

## Question: "how many tests passed / timed out before and after time T, and over what window did the TIMEOUTs start?"

- Command: `profiler-cli thread markers --search "name:test" --list --limit 0 --session ... --json`, then a Python script grouping `data.status` by `start < T`.
- Expected: a way to group Test markers by a payload field within a zoom range (`--group-by field:status` with `zoom push 0,T` and `zoom push T,end`).
- Got: zoom includes markers overlapping the range, so every 45 s TIMEOUT that started earlier is counted in the later range too; no "started within range" option.
- Workaround: script over `--json`.
- Could show: an option to restrict to markers whose start is in the zoom range, combined with `--group-by field:status`.
