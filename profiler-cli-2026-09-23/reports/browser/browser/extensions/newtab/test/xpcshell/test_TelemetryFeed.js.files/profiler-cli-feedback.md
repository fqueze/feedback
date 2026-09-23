## Per-subtest durations in a run (question: "how long did each subtest take, and where was it when killed")

- Command: `profiler-cli thread markers --session browser-test_TelemetryFeed.js-1 --search test_TelemetryFeed --list --limit 0`
- Expected: timestamps precise enough to subtract consecutive `Starting <subtest>` markers (xpcshell subtests last 10-400 ms).
- Got: `t=5m47s`, `t=6m29s` — rounded to whole seconds, so consecutive subtests show the same time.
- Workaround: `--json` and a Python script over `flatMarkers[].start` to compute gaps between `Starting` markers.
- What the output could have shown: millisecond start times in `--list` (at least within a zoom), or a relative time from the zoom start.

## Machine CPU during one test (question: "was the machine saturated while this test ran")

- Command: `profiler-cli zoom push m-1` then `profiler-cli thread markers --search "name:CPU Use"`
- Expected: some summary of the `cpuPercent` field over the zoom (mean/median/min).
- Got: only the count and interval durations of the `CPU Use` markers.
- Workaround: `--list --limit 0 --json` and averaging `data.cpuPercent` in Python.
- What the output could have shown: numeric-field stats per marker name within the view, e.g. `cpuPercent: mean 99.4%, min 72.6%`.

## `cpuPercent` is a string in `--json` (question: "was the machine saturated while this test ran", review)

- Command: `profiler-cli thread markers --session browser-review-test_TelemetryFeed.js-1 --search "name:CPU Use" --list --limit 0 --json`
- Expected: `data.cpuPercent` as a number, like `data.user` and `data.system` next to it.
- Got: `"cpuPercent": "98.9%"` (and `user_pct` etc. as strings), so a script averaging it fails on the first try.
- Workaround: strip the `%` and parse.
- What the output could have shown: the raw number in `data`, with the `%` only in `formattedValue`.
