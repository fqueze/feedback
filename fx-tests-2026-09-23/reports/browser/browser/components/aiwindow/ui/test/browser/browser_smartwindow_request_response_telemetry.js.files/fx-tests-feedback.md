## Question: "what are all of this test's failures, on every tree?"

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_request_response_telemetry.js` (also `--history`, `--task-ids`)
- Expected: the dominant failure of the test's bug (50 of 58 sheriff annotations in 21 days: `records offline ... uncaught rejection: Error: Request timed out.`, perma-failing on mozilla-esr153 / mozilla-release Windows) to show up, or at least a note that the verdict covers trunk only.
- Got: verdict "intermittent, 99.83%", Issues listing only trunk Win32 OOM/timeouts, and no hint that another tree fails it every run. `fx-tests intermittent --bug 2030788` with defaults exited 2 ("no sheriff annotations ... on trunk"), which reads as "nothing annotated", when 22 esr153 annotations existed in that same 7-day window.
- Workaround: `fx-tests intermittent --bug 2030788 --tree all --since 21 --limit 0`, then `fx-tests task <esr taskId>` per job.
- What would have answered it: `fx-tests test` naming the trees its data covers in the header, and `intermittent --bug` falling back to (or at least mentioning) `--tree all` when trunk has zero rows.
