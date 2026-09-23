## Question: in what order, to the millisecond, did the test's log lines and the DOM events around the failure happen?

- Command: `profiler-cli thread markers --search "eventType:TabClose,eventType:TabSelect,eventType:transitionend,...,TEST-UNEXPECTED" --list --limit 0 --session <id>`
- Expected: a start time precise enough to order events that are a few ms apart.
- Got: every row says `t=1m23s`; the whole 100 ms race (TabClose, TabSelect, transitionend, TabSelect, TEST-UNEXPECTED-FAIL) is one indistinguishable timestamp. List order is start order, but gaps and durations (was the check before or after the transitionend? how long after the TabClose?) are invisible.
- Workaround: `--json` piped to a python script printing `start` and `start+duration` in ms.
- What the default output could show: ms-resolution start (and end for intervals) in `--list`, at least when the listed markers span less than a few seconds, or relative to the first listed marker.

## Session vanished mid-investigation

- Command: any query on `--session browser_multiselect_tabs_close.js-1` after ~5 minutes of use.
- Got: `Session ... is not reachable. Nothing is accepting connections on ...sock. The daemon exited without cleaning up.`, then `Unknown session ...: no metadata found`. The session's log ends with an ordinary `Received message: command`, no error. Machine had 57 GB available.
- Workaround: reload the profile (new marker handles, redo the queries).
- Possibly another agent's stop; if so, the owner check did not protect it. If it was a crash, the log should say so.

## Load timeout on a large per-test profile

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Mlub5lrOTmODOFyzr3UHXg/runs/0/artifacts/public/test_info/profile_browser_multiselect_tabs_close.js.json --session ...-3`
- Expected: load returns when loaded.
- Got: `Error: Profile load timeout after 60000ms`, then `Profile still loading, try again shortly`; the daemon finished about a minute later.
- Workaround: poll `thread select t-0` until it succeeds, or `PROFILER_CLI_LOAD_TIMEOUT_MS=240000`.

## Question (review): which marker does a link's `marker=N` point to?

- Command: `profiler-cli marker info 1656796 --session <id>` (N taken from a report's link).
- Expected: the marker at profile index N, or a way to look it up by index.
- Got: `Invalid marker handle 1656796: expected a handle like m-42`. `thread markers --list --json` `flatMarkers` entries carry no `markerIndex` either.
- Workaround: `thread markers --search` for the quoted marker, then `marker info <handle> --json` per candidate and compare `markerIndex`; one of 12 links pointed at the second of two same-named `transitionend` markers 1.3 ms apart, which only this comparison told apart.
- What the output could show: `markerIndex` in `--list` rows (or `--json` flatMarkers), or `marker info --index N`.
