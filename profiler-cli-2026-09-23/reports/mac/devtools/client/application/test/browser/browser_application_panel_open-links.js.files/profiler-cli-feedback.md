## Default session directory not writable in the agent sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster URL> --session <name>`
- Expected: the profile loads.
- Got: `Error: Cannot create the profiler-cli session directory /Users/florian/.profiler-cli. Permission denied.` The message was clear and suggested `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`.
- Workaround: export `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command. `profile-link.py` honours the same variable. The subagent brief does not mention it, so each agent finds out by failing once.

## Review: a session loaded from a profiler link cannot build links (review-browser_application_panel_open-links.js)

- Command: `profiler-cli load "https://profiler.firefox.com/from-url/<encoded taskcluster URL>/marker-table/?marker=434815&thread=0&v=17" --session <s>` (as `review-brief.md` says, to check a link's thread and marker), then `python3 profile-link.py --session <s> --marker m-807`.
- Expected: a link for the new observation, as the from-url link carries the raw artifact URL.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.`
- Workaround: stop and reload each profile from its Taskcluster URL, then find the handles again by `markerIndex`. That is a second 0.6 GB load per profile. Either profiler-cli could record the decoded `from-url` artifact URL when loading a profiler link, or profile-link.py could decode it itself.

## Review: "when was this runnable queued, and by what?" (review-browser_application_panel_open-links.js)

- Question: when was the `exports.makeInfallible/<[ThreadSafeDevToolsUtils.js]` runnable that delivered the root actor's hello packet dispatched, and from which task? The same for `NotifyUnhandledRejections` → `dom::AsyncErrorReporter` → `LogMessageRunnable`, to find out why a console message came 500 ms after its throw.
- Command: `thread markers --search <task id> --list`, which matches the `TaskController::AddTask (flow=<id>)` marker. Then `marker info --json` on both markers, comparing `start` and `markerIndex` against the enclosing `Runnable`.
- Cost: task ids are reused addresses. The same flow id matches unrelated runnables seconds apart, so every step needed a time comparison by hand. `thread markers --list --json` has no `markerIndex`, so one `marker info` call per handle was needed.
- What would answer it: `marker info` on a `Runnable` could show its dispatch. That is the closest preceding `AddTask` with the same flow, the time it was queued, and the runnable or marker it was queued from.

## Review: default thread is not the parent main thread

- Command: `profiler-cli load <taskcluster per-test profile URL> --session <s>`.
- Got: `Selected thread: t-70 (GeckoMain, Privileged Content)` in one profile and `t-78` in another, although t-0 (Parent Process GeckoMain) has 20 times more markers and is where the test log is. A `thread select t-0` step is needed every time.
