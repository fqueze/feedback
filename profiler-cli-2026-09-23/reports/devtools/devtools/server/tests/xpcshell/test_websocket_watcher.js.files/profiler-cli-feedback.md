## Question: "exact start time of a listed marker, to order it against another event ~2 s earlier"

- Command: `profiler-cli thread markers --search test_websocket_watcher --list --limit 0 --session test_websocket_watcher.js-1` (3m37s resource-usage profile).
- Expected: start times precise enough to compare with the first launch failure (131.676 s) and the `will retry` 50 ms later.
- Got: `t=2m14s` for every row; the test start (133.599 s) and its `will retry` (133.649 s) print identically.
- Workaround: `--json` and read `flatMarkers[].start`. The list could print ms precision when the rows' times collide, or always for instants.

## Question: "which instant markers fall inside this 5 ms window"

- Command: `profiler-cli zoom push 178.650,178.655` then `thread markers --list --limit 20`.
- Expected: the markers logged in that window (the replayed full log of one test).
- Got: 288 rows, led by every interval marker spanning the window (`run-tests` phase, `parallel`, hundreds of 45 s `test` markers); the instants inside were past the limit.
- Workaround: `--json` and filter on `start` within the window. An option to list only markers starting inside the zoom would answer it.
