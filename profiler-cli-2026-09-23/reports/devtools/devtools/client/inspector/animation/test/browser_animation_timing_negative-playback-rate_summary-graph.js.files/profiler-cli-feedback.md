## Timestamps in `thread markers --list` and `marker info` collapse to whole seconds in long profiles

- Command: `profiler-cli thread markers --session nprs-2 --category Test --search negative-playback-rate_summary --list --limit 0` (profile is 1m46s long, task exVMldenTJ-LuY0CjZgSaw)
- Expected: per-marker times with at least ms resolution, as in a 3 s profile (`t=2.741s`).
- Got: every row shows `t=1m46s`; `marker info m-11` also says `Time: 1m46s (instant)`. The test's whole log (350 ms) is indistinguishable, and the order of markers from different threads cannot be read.
- Workaround: `marker info m-4 m-11 m-77 --json` and read `start` (ms). Question it could have answered by default: "at what time, to the ms, did this marker happen".
