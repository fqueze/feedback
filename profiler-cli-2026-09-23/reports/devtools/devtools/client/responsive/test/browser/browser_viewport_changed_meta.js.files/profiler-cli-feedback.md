## Zoomed `thread markers --list` is crowded by IPC markers that started long before the zoom

- Command: `profiler-cli zoom push 4.842,4.977` then `profiler-cli thread markers --min-duration 3 --list --limit 60` on the parent main thread.
- Expected: the markers of those 135 ms.
- Got: the first 50+ rows were IPCIn/IPCOut markers starting at t=2.236s with 2.6 s to 6.4 s durations (long-lived async messages that merely overlap the range), pushing the in-range runnables past the limit.
- Workaround: `--search name:Runnable --min-duration 1`, which found the 85 ms runnable directly.

## `thread samples --include-idle` does not say how many samples were idle

- Command: `profiler-cli thread samples --include-idle` in a 134 ms zoom.
- Expected: the idle share, to tell "blocked" from "idle".
- Got: "Categories (9 running samples)" with no idle row, so it was unclear whether the thread was idle or the sampler sparse. The `Awake` markers answered it.

## `marker stack` on a RefreshDriverTick prints its cause stack without saying so (review-browser_viewport_changed_meta.js)

- Command: `profiler-cli marker stack m-254` for the parent's RefreshDriverTick at t=4.976s.
- Expected: either the tick's own stack, or a label saying this is the stack that requested the tick.
- Got: "Captured at: 4.839s" with a `Task RefreshDriver::EnsureTimerStarted::catch-up` stack. That belongs to the previous tick, 137 ms earlier. The report then gave that runnable to the wrong tick.
- Workaround: the `marker stack` of a Reflow (sync) inside the tick showed its real runnable (`RefreshDriverVsyncObserver::NotifyVsyncTimerOnMainThread`).
