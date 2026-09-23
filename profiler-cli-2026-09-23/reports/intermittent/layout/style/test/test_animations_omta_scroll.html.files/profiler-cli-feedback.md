## Ordering markers less than 1 ms apart (test_animations_omta_scroll.html)

- Question: in which order, and how far apart, did three Compositor runnables run (SetAsyncScrollOffset, GetAnimationValue, ScheduleComposition), all within ~2 ms?
- Command: `profiler-cli thread markers --search "Runnable,SampleAnimation" --list --limit 0 --session <s>`
- Expected: start times precise enough to order and subtract them (e.g. 17.8076s, 17.8081s, 17.8094s).
- Got: every row printed `t=17.808s`; `marker info` also prints `Time: 17.808s - 17.808s (56.193μs)`.
- Workaround: `--json` and a Python filter on `flatMarkers[].start` (ms, full precision). A `--precision` flag, or printing more digits when the view is zoomed to a few ms, would have answered it directly.

## Where the mochitest-plain TEST-* log lives

- Command: `profiler-cli thread markers --category Test --search test_animations_omta_scroll --list --limit 0` on the parent GeckoMain.
- Expected: the test's TEST-PASS / TEST-UNEXPECTED-FAIL markers.
- Got: 0 markers; for mochitest-plain they are on the content process GeckoMain (`http://mochi.test`).
- Workaround: `profiler-cli profile markers --search <test name>` found the thread. Pointing to it when a `--search` on one thread returns 0 but other threads match would save the round trip.
