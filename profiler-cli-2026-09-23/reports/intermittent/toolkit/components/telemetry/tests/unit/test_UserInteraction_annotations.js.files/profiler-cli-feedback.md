## Question: the exact time between two markers on different threads

- Command: `profiler-cli thread markers --thread t-13 --list` and `--thread t-15 --list`, then `marker info m-109 m-105 --json | python3 ...`
- Question: was BHR's 128 ms timer fired before 128 ms had elapsed since the last `NotifyActivity`? The answer hinges on ~100 µs.
- Got: the list prints `t=130.32ms` / `t=2.435ms` (rounded), so the difference is 127.885 ms +/- 10 µs, not enough to be sure; `marker info` without `--json` prints the same rounding. Had to read `.start` from `--json`.
- Could have shown: full-precision start (and end) in `marker info` text output, or a `marker delta m-a m-b` that prints the exact interval between two handles.

## `zoom push` past the end of the profile is accepted silently

- Command: `profiler-cli zoom push 2.1,1100 --session ...` on a 29.9 s profile (I meant ms, my mistake).
- Expected: a warning that the range ends 1070 s after the profile does.
- Got: the zoom was accepted; the header showed `View: ts-6→ts>68 (18m18s)` and later marker queries returned almost nothing, which looked like "no markers" rather than "wrong range".
- Workaround: re-zoom with seconds.

## Question: the exact value of a marker field (review-test_UserInteraction_annotations.js)

- Command: `profiler-cli thread markers --list` and `profiler-cli marker info m-1` (session review-test_UserInteraction_annotations.js-1, Timer thread)
- Question: what `hangTime` did BHR compute? It is 8192 minus the `Delay` of the timer it re-armed, so the answer depends on the fraction of a millisecond.
- Got: both text outputs print `Delay: 8,064ms` / `❌ 8,064ms`. The real values (8064.110 vs 8063.893, one side of 128 ms or the other) were only in `marker info --json` `.fields[].value`.
- Could have shown: full precision for duration fields in `marker info` text output, at least when the value is not a whole number.
