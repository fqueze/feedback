## Marker times in text output are rounded to the second late in a profile

- Command: `profiler-cli thread markers --session S --category Test --search browser_dbg-sources-with-many-actors --list --limit 0`
- Expected: sub-millisecond start times (the question was the order of dispatches 0.3 ms apart, and how long a `waitFor` lasted).
- Got: `t=7m36s` for every marker in a 7m42s profile, so the markers of a whole second look simultaneous.
- Workaround: `--json` piped through a python script that prints `start`. The question "in what order, and how far apart, did these markers happen" needs ms (or µs, when zoomed to a few ms) in the default output.

## "Which threads lack marker X" has no answer without a script

- Command: `profiler-cli profile markers --search RegisterDone --session S`
- Question: which DOM Worker threads never got `PRemoteWorkerDebugger::Msg_RegisterDone`, when every other one did.
- Got: the matching threads (145 of 196), truncated. Diffing them against `thread list` meant using `--json` and a shell diff, which I got wrong the first time.
- Could show: an option on `profile markers` that lists the threads (filtered by name, e.g. `--thread-name "DOM Worker"`) with zero matches.

## Reviewer: a loaded link's range stays as a zoom level

- Command: `profiler-cli load --session S '<link with range=9553000u5000>'`, then `zoom push` / `zoom pop` of my own range, then `thread markers --session S --thread t-0 --search "three source actors" --list`.
- Expected: after popping my zoom, the full profile.
- Got: the link's 5 ms range was still applied. The search returned only the `waitForCondition` marker and not the `TEST-UNEXPECTED-FAIL` 5 s later. The header shows the view, but it's easy to miss.
- Workaround: a second `zoom pop`. Printing "(view restricted to the loaded link's range)" next to the match count would have caught it. (I hit both issues above again during the review.)
