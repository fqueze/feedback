## "Which idle tasks queued before this point never ran?" needed a script

- Command: `profiler-cli thread markers --search 'ChromeUtils::IdleDispatch,name:startupIdleTask,name:perWindowIdleTask' --list --limit 0 --json`, then a Python script to sort by start and pair each `Runnable ChromeUtils::IdleDispatch` with the `startupIdleTask` / `perWindowIdleTask` marker it contains.
- Question: after `StartupTelemetry.browserIdleStartup` queued its idle tasks, did any of them run before the main thread hung? The answer is "every idle runnable after it is accounted for by another task's marker", which is a nesting question.
- Expected: some way to list markers together with the markers they contain (or their parent marker), e.g. `--list --nest` or a `contains:` column, so a runnable with no named child stands out.
- Got: a flat list where the runnables and the task markers interleave, 100+ rows to pair by eye.
- Workaround: the script above.


## (review) A raw-URL load selects a content process, and marker queries then silently find nothing

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/ETAtj63lR0aIgKZp9Vk0Bw/runs/0/artifacts/public/test_info/profile_browser_favicon_svg.js.json --session S`, then `thread markers --search browser-startup-idle-tasks-finished --list`
- Expected: the parent process main thread selected, as the same profile's `...&thread=0` link does, or a note that the selected thread is not the parent's.
- Got: `Selected thread: t-21 (GeckoMain, http://mochi.test (7/10))`, and `No markers match the specified filters.` for a marker the parent thread has. The load's status line said so, but I had cut it with `tail -1`.
- Workaround: `thread select t-0`, one reload.
- The question it should answer: "does this parent-process marker exist". A no-match on a content thread reads as "absent".
