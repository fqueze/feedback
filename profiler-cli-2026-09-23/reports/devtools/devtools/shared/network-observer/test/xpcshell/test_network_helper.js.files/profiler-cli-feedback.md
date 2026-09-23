## Question: "at which millisecond did each harness log line happen, and how far apart are they?"

- Command: `profiler-cli thread markers --list --limit 0 --session <id>` after `zoom push m-1` (a 2.5 s `test` marker in an xpcshell resource-usage profile).
- Expected: start times precise enough to order and space the ~20 INFO/DEBUG lines inside the window (the diagnosis rests on "0.66 s between the last setup step and the exception").
- Got: every row printed as `t=10m34s` / `t=10m35s` / `t=10m36s`, second resolution. The ms were only in `--json` (`flatMarkers[].start`), so I wrote a script over it.
- Could show: ms precision (or `t=634.060`) when the view spans less than a few seconds, or a `--precise-times` flag.

## Question: "which Android process slot did this xpcshell test run in, and what else ran in that slot?"

- Command: `profiler-cli thread markers --list --search "name:INFO,name:DEBUG,name:test" --json` over a 60 s zoom, then a script extracting `XpcshellTestRunnerService$iN` and `_TEST_NAME` from each `launch_application` message, and `…:xpcshellN:pid` from each `get_process_list` DEBUG marker (each one a ~375-entry Python list literal, cut off in the default output).
- Could show: nothing generic, but the default output cutting `get_process_list` at 400 chars made the only useful part (the `xpcshell` entries at the end) invisible; a `--search`-aware excerpt of long messages, centred on the match, would have shown them.
