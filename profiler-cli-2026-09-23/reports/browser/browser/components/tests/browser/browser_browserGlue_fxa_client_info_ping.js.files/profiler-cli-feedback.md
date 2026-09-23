## Loading a per-test CI profile selects a WebExtensions main thread, not the parent main thread

- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/JTA0f1gWTUGi6dPoZZKQiQ/runs/0/artifacts/public/test_info/profile_browser_browserGlue_fxa_client_info_ping.js.json" --session ...` then `thread markers --category Test --list --limit 0`
- Expected: t-0 (GeckoMain, Parent Process) selected, as it was for the other two profiles I loaded (and as `profile info` lists it first by CPU).
- Got: `t-18 (GeckoMain, WebExtensions)` selected, so the Test-marker query returned "No markers match", which reads like "the test log is not in this profile".
- Workaround: `thread select t-0`.

## "Was the main thread asleep in this range, and for how long?" has no direct answer (review)

- Command: `profiler-cli zoom push 1.354,1.774 --session …` then `thread samples --include-idle --limit 15`
- Question: whether idle-priority runnables were starved by a busy main thread or the thread slept with them pending.
- Got: "10 running samples" and the running call tree; no count or duration of idle/sleeping time in the range.
- Workaround: `thread markers --search Awake --list` and subtracting the `Awake` intervals by hand to find the sleep gaps. A "sleeping N ms of M ms, gaps: …" line in `thread samples --include-idle` or `profile info` for the zoomed range would have answered it.

## profile-link.py refuses a session loaded from a profiler.firefox.com link (review)

- The review brief says to `profiler-cli load <link>` the report's links; `profile-link.py --session S --marker m-465` on that session then printed "A link needs the raw artifact URL" and no link.
- Workaround: stop, and reload the raw Taskcluster URL to build new links, which costs a full reload per profile. `profile-link.py` could take the artifact URL out of the `from-url/` link.
