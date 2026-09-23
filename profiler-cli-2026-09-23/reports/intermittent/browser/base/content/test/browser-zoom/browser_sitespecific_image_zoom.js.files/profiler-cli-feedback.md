
## review-browser_sitespecific_image_zoom.js: "which task dispatched this runnable, and when?"

- Command: `profiler-cli thread markers --session <s> --search <task id from a Runnable row> --list --limit 0`
- Expected: the `TaskController::AddTask` / `PutEvent` that queued this runnable, and ideally the runnable running at that moment.
- Got: every marker that shares the flow id over the whole profile. Flow ids are recycled addresses, so one id mixes unrelated runnables (FlushRejections, FullZoom notify, run[browser-test.js]…), and `marker info` on a Runnable gives no dispatch time.
- Workaround: take the AddTask just before the Runnable, then zoom there to find the enclosing task. A `marker info` field such as "queued at t, from <runnable>" would answer it in one call.
