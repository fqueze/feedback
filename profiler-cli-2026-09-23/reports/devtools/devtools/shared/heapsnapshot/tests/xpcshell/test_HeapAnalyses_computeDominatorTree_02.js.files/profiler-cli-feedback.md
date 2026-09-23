## Marker list times are rounded to the second

- Question: at what exact time did each harness log line (`INFO`/`DEBUG` TestStatus markers) happen, so I could line them up with logcat and the kernel log, and order `get_process_list` listings 100 ms apart?
- Command: `profiler-cli thread markers --session <s> --search computeDominatorTree_02 --list --limit 0`
- Expected: start times with ms precision, e.g. `t=185.315s`, since instant markers in a 22-minute resource-usage profile are often 50–150 ms apart.
- Got: `t=3m5s`, `t=3m6s`. Five markers inside one second all showed the same time, and `marker info` also prints `Time: 3m5s (instant)`.
- Workaround: `--json` piped through a script that prints `start/1000` with 3 decimals. This was needed for every marker query in the investigation.
- The default output could have printed `3m05.315s` (or seconds with 3 decimals) whenever the profile is longer than a minute.

## `get_process_list` payloads: which pids appeared or vanished between listings

- Question: which processes died between two consecutive `get_process_list` DEBUG markers (Android xpcshell harness)?
- Command: `profiler-cli thread markers --search get_process_list --list --limit 0`
- Got: each ~330-entry listing truncated to its first ~150 characters (kernel threads), so the test_runner entries were never visible.
- Workaround: `--json`, then `ast.literal_eval` of each label and a diff of consecutive listings.
- This is harness-specific, so it may not belong in the tool. A `--full` or `--width` option for `--list` labels would at least have shown the entries.
