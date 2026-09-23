## Question: in what order, to the millisecond, did these markers happen?

- Command: `profiler-cli thread markers --category Test --search browser_application_panel_open-links.js --list --limit 0 --session <id>` (and the same with other `--search` terms)
- Expected: a start time precise enough to order markers that are milliseconds apart (e.g. `t=74.409s`).
- Got: `t=1m14s` on every row: in a 1m15s profile, everything of interest shares the same one-second label, so "unload before or after the hello packet delivery" could not be read from the list. The list is also not strictly chronological (m-11 TEST-PASS appears between m-17 and m-18 with the same label).
- Workaround: `--json` and a script printing `flatMarkers[].start` (ms) and `duration`.
- Could have shown: start times with millisecond precision (at least when the profile is longer than a few seconds), and durations in ms for interval markers.

## Question: which thread am I querying after loading a mochitest profile? (review)

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<id>/runs/0/artifacts/public/test_info/profile_<test>.json --session <s>`
- Expected: the parent-process GeckoMain (t-0) selected, since that is where the test log and almost every question is.
- Got: `Selected thread: t-16 (GeckoMain, WebExtensions)` (and t-75 on another profile). Any `thread markers` call without `thread select t-0` first silently runs on the wrong thread.
- Workaround: `thread select t-0` after every raw-URL load.
- Could have shown: parent GeckoMain selected by default, or a warning on load when the selected thread has no `Test` markers but another one does.
