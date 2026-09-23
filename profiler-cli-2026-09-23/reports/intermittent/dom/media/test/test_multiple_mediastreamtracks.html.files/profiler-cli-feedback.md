## Log (INFO) test markers print "(empty)" instead of their message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_multiple_mediastreamtracks.html-1` (content process main thread t-17 of https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/GnucYus1Sguv25abVn9Esw/runs/0/artifacts/public/test_info/profile_test_multiple_mediastreamtracks.html.json), and `profiler-cli marker info m-1803`.
- Expected: `INFO  t=7.719s  Unmuting audio` — the test's own `info()` log lines.
- Got: `INFO  t=7.719s  instant  [(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in `marker info`. `marker info --json` shows `"value": "Unmuting audio"` but `"formattedValue": "(empty)"` for the `message` field (and `"value": "INFO"`, `"formattedValue": "(empty)"` for `level`). So every INFO line of the test log is unreadable in the default output; TEST-PASS markers (type TestStatus) are fine.
- Workaround: `marker info <m> --json` one marker at a time and read `fields[].value`.

## Question: "which IPC messages, by type, did this actor exchange in this window?"

- Command: `profiler-cli thread markers --search PCameras --list --limit 0 --session test_multiple_mediastreamtracks.html-1` (zoomed to the test).
- Expected: one row per IPC marker with its message type, e.g. `IPCOut t=10.540s PCameras::Msg_StartCapture`, `IPCIn t=10.541s PCameras::Msg_DeliverFrame` — the search matched on that very field.
- Got: rows reading only `IPCIn` / `IPCOut` with a blank description column, so the sequence AllocateCapture / StartCapture / DeliverFrame / StopCapture, which was the whole diagnosis, was invisible. `--json` flatMarkers did not carry the type either.
- Workaround: `profiler-cli marker info m-956..m-1092 --json` and a Python script decoding the concatenated JSON records and printing `fields[key=messageType].value`. Showing `messageType` (and direction) as the description of IPC markers in `--list` would have answered it directly.

## Question: "which marker does a link's `marker=N` point to?" (review-test_multiple_mediastreamtracks.html)

- Command: `profiler-cli load <link with marker=6299>`, then looking for that marker. `load` opens the thread and range but not the marker, and no command goes from a `markerIndex` to an `m-` handle.
- Expected: `profiler-cli marker info --index 6299` (or `load` printing the selected marker's handle), so a link can be checked in one step.
- Got: I had to bisect with a script: list the markers of a zoomed range with `--json`, then call `marker info <m> --json` on each one to read `markerIndex`. This took about 15 calls to find that 6299 was a `beforeunload` DOMEvent and not the `TEST-UNEXPECTED-FAIL` the report meant (index 123405).
- Also: in this run, `thread markers --search PCameras --list --json` did carry `fields[messageType]` in `flatMarkers`, so the earlier note that it lacked the type did not reproduce. The default `--list` still shows only `IPCIn`/`IPCOut`.
