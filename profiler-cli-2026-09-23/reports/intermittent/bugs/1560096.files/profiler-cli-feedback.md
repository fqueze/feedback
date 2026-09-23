## review-1560096: checking a report's quoted marker times and link indices
- Question: "at what time, to the ms, did this marker happen, and what is its markerIndex?" (to check a report quoting 256.607 s vs 256.598 s, and a link's `marker=N`).
- Command: `profiler-cli thread markers --search SUBPROCESS_ABNORMAL_ABORT --list` and `profiler-cli marker info m-1`.
- Expected: ms-precision start time (e.g. 256.607s) and the markerIndex in the default output.
- Got: `t=4m17s` / `Time: 4m17s` only, which can't separate markers 4 ms apart, and no markerIndex.
- Workaround: `marker info m-1 m-2 ... --json` piped through a python script that prints `markerIndex` and `start`.
