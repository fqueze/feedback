## Marker times in `--list` are rounded to the second: ordering within a 20 ms race is unreadable

Question: "in what order, to the millisecond, did ShowOverlay / getFullPageBounds / rAF happen across two processes?"

Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli thread markers --session browser-full_page-1 --search "DOMEvent,NotifyObservers,Screenshots" --list --limit 0` (with `zoom push 76.425,76.455`)

Expected: a start time with ms (or sub-ms) precision, at least when the view is zoomed to a few tens of ms.
Got: every row reads `t=1m16s`, so a 30 ms window shows 97 rows with identical times.
Workaround: `--json` piped to a python script printing `start/1000` with 4 decimals. Needed for every timeline in this report (three profiles, two threads each).

## `load` selected the WebExtensions main thread, not the parent main thread

Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/LY5puuNsS1qB2cQ_RcIotg/runs/0/artifacts/public/test_info/profile_browser_screenshots_test_full_page-2.js.json --session browser-full_page-2`
Expected: t-0 (GeckoMain, Parent Process) selected, as it was for the `-1` profile of the same job.
Got: `t-9 (GeckoMain, WebExtensions)` selected; `thread markers --category Test --search <test>` then returned "No markers match", which reads like the test log is missing.
Workaround: `thread select t-0` explicitly.

## (review) Same rounded `t=1m16s` times hit again while checking the report

Command: `profiler-cli thread markers --list --limit 0 --search "Screenshots:,PVsync,..."` zoomed to 76.40–76.47 on each of 4 profiles.
Got: identical `t=1m16s` on every row. Workaround again: `--json` into a script printing `start` to 0.01 ms (kept as `review-walk.py` in this directory).
