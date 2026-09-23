## Ordering markers that fall in the same second (browser_inspector_picker-page-reload.js)

- Question: in which order, to the millisecond, did the test log, the parent's JSActor messages and the content process's IPC tasks happen during a ~50 ms race?
- Command: `profiler-cli thread markers --session <s> --category Test --search <test> --list --limit 0` (and the same with `--search "DevTools:RDP Actor,NotifyObservers,..."` on a content thread)
- Expected: a start time per row with sub-millisecond precision, since a per-test profile's whole test often fits in one or two seconds.
- Got: every row printed as `t=1m43s`, so all 66 test-log rows and the content-process markers were indistinguishable in time. `marker info` also prints `Time: 1m43s`.
- Workaround: `--json` piped through a script printing `start/1000` with 4 decimals and `duration`, plus `data.innerWindowID` and IPC `messageType`, which the list also does not show.

## Default thread after `load` is not the parent main thread

- Command: `profiler-cli load <taskcluster url of profile_browser_inspector_picker-page-reload.js.json> --session <s>` then `profiler-cli thread markers --session <s> --category Test --search picker-page-reload --list --limit 0`
- Expected: the parent process GeckoMain selected by default, as happened for the other profile of the same job type (t-0), or a hint that matches exist on other threads.
- Got: the session selected t-122 (GeckoMain, Web Content (7/7)), and the query returned 0 markers with no hint.
- Workaround: `profile info --search GeckoMain`, then `thread select t-0`.

## IPC tasks: which request failed

- Question: which RDP request to a content process failed?
- Got: failed requests leave no `DevTools:RDP Actor` marker (Actor.js only marks successes), so the failed one was found as the `ReceiveMessage DevToolsProcessParent:packet` that had no matching success marker, by lining up two JSON dumps by time. Not a tool bug; noting that a "ReceiveMessage without a following RDP marker" view had to be built by hand.

## Review: checking that a report's `marker=N` links point at the quoted markers (review-browser_inspector_picker-page-reload.js)

- Question: does the marker a report quotes have `markerIndex` N, for 25 links across two profiles?
- Command: `profiler-cli marker info m-760 m-869 ... --session <s>`
- Expected: the marker index (the `marker=N` of a profiler link) and a sub-millisecond start time in the default output, since matching a link to a marker is exactly what these fields are for.
- Got: neither; `Time: 1m43s - 1m43s`, no index. Only `--json` has `markerIndex` and `start`.
- Workaround: `marker info <m> --json` in a loop piped through a one-line Python script printing `markerIndex`, `name` and `start/1000`.
