## Session name following the brief's `<report name>-1` convention is rejected for long test names

- Command: `PROFILER_CLI_SESSION_OWNER=browser-browser_ext_webNavigation_onCreatedNavigationTarget_subframe_window_open.js profiler-cli load <taskcluster URL> --session browser-browser_ext_webNavigation_onCreatedNavigationTarget_subframe_window_open.js-1`
- Expected: the session loads.
- Got: `Error: The Unix socket path for this session is 118 bytes, over this platform's 107-byte limit`, after starting the download.
- Workaround: a shorter session id (`browser-subframe-wo-1`). The error is clear, but it would be cheaper if the check ran before the download, or if the socket name were derived from a hash of the session id so any id works.

## Question: "at what time, to the millisecond, did each line of the test's log happen?"

- Command: `profiler-cli thread markers --category Test --search subframe_window_open --list --limit 0 --session browser-subframe-wo-1` on a 7-minute profile.
- Expected: start times precise enough to order the lines and measure the vsync wait (ms).
- Got: `t=6m56s` for all 33 lines of a test that ran in under a second, and `marker info` also prints `Time: 6m56s (instant)`. Needed `--json` and a script to read `start`.
- Could have shown: seconds with 3 decimals (`t=416.030s`) in `--list` and `marker info`, at least when the profile is longer than a minute.

## (review) Second load of the same profile times out at 60 s

- Command: `PROFILER_CLI_SESSION_OWNER=browser-review-subframe_window_open profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/QpSrFmkpR1Gt5mBA-g7xLw/runs/0/artifacts/public/test_info/profile_browser_ext_webNavigation_onCreatedNavigationTarget_subframe_window_open.js.json' --session browser-review-subframe_window_open-3`, with one other session still loaded.
- Expected: the load succeeds, as the same profile's first load (from its profiler.firefox.com link) had.
- Got: `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`.
- Workaround: `PROFILER_CLI_LOAD_TIMEOUT_MS=400000`. A 7-minute CI profile regularly takes longer than 60 s to download and process; the default could be larger, or the timeout reset while download progress is being made.

## (review) profile-link.py rejects a session loaded from a profiler.firefox.com link

- Command: `python3 $D/profile-link.py --session browser-review-subframe_window_open-1 --thread t-0 --range 416.374,425.263 --panel marker-chart --search Vsync`, on a session loaded from the report's link, as `review-brief.md` says to load it.
- Expected: a link.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.`
- Workaround: reloaded the raw Taskcluster URL into a third session (and hit the timeout above). The script could extract the `from-url` part of a profiler link, since that is the raw artifact URL.
