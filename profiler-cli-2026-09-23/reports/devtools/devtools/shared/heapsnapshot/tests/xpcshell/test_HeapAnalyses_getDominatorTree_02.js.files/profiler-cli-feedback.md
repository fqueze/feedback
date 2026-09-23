## Marker times in `--list` and `marker info` are rounded to the second in a 22-minute profile

- Command: `profiler-cli thread markers --session <s> --search test_HeapAnalyses_getDominatorTree_02 --list --limit 0` and `profiler-cli marker info m-1 m-3 m-6`, on the resource-usage profile of XWcdVqz-T7qkqWmxyZy4wg (22m52s long).
- Expected: times precise enough to order harness log lines that are milliseconds apart (the ps listing, then the error message 4 ms later).
- Got: `t=14m58s`, `t=15m`, `t=15m1s`; `marker info` prints `Time: 15m (instant)`. The failing sequence spans 0.4 s, so every relevant line reads `t=15m`.
- Workaround: `--json` and `start / 1000` in a script. A `--precise` flag, or ms precision in `marker info` at least, would do.

## Question: "which processes did each `get_process_list` line list, and when did a pid change name?"

- Command: `profiler-cli thread markers --session <s> --search get_process_list --list --limit 0 --json` (1,320 markers, 75 MB), then a Python script parsing the `[[pid, 'name', 'user'], ...]` payload of each DEBUG message.
- The default output truncates each message to the terminal width, so the list of process names, the only part that matters, is never visible; the JSON has it but only as one Python-repr string. This is harness-specific, so perhaps not the tool's job, but a `--full-messages` (like fx-tests has) plus a `--grep-in-message <regex>` that prints only matching substrings would have answered "which `:xpcshellN` names are in each listing" without a script.

## Question: "which slot (XpcshellTestRunnerService$iN) did each test run in?"

- Command: zoom windows dumped with `thread markers --list --limit 0 --json`, then a script matching `launch_application` lines (`$iN`, `_TEST_NAME`) to `test` interval markers.
- The same `--grep-in-message` / full-message option would have shown the `$iN` and `_TEST_NAME` fields, which sit past the cut of each 3 KB message.

## (review) Question: "is handle m-N the marker a link's `marker=<index>` points at?"

- Command: `profiler-cli marker info --session <s> m-446 m-3`.
- Expected: the marker index, since profile links carry `marker=<index>`, and checking a link means matching the two.
- Got: no index, and `Time: 15m (instant)` again. I had to run `--json` and read `markerIndex`, once per handle in a shell loop.
- Would help: print `Index: 54231` and the time to the millisecond in the default `marker info` output.
