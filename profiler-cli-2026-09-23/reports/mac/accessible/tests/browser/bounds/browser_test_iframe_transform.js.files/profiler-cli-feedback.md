## Question: "which of two markers on different threads came first, when they are < 1 ms apart?"

- Command: `profiler-cli thread markers --session S --list --limit 0 --search "Runnable,Query"` on two threads.
- The list prints start times rounded to the millisecond (`t=15.106s`) and durations but no end times, so "did the `PBrowser::Msg_ChildToParentMatrix` runnable start before or after the `PWindowGlobal::Msg_RawMessage` task that read the bounds ended" (0.05-1.1 ms apart in this test) was unanswerable from the default output.
- Workaround: `--json` and a script printing `start` and `start + duration` with microseconds. `marker info` also rounds (`15.103s - 15.105s`).
- Could have shown: sub-ms start (and end) times in the list when the view is zoomed to under a second, or when neighbouring rows share the same millisecond.
