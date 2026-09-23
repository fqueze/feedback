## `thread markers --list` times have whole-second precision on long profiles

- Command: `profiler-cli thread markers --session test_HeapSnapshot_takeCensus_09.js-1 --search takeCensus_09 --list --limit 0` (a 23-minute resource-usage profile)
- Expected: start times precise enough to order markers a few ms apart (e.g. `t=683.644s`).
- Got: `t=11m24s` for markers at 683.644 s and 683.928 s alike. The failure turns on a `get_process_list` 26 ms before the failure message, so the list could not order them.
- Workaround: `--json`, then `flatMarkers[].start` in a script.

## Question needing a script over `--json`: the name history of each pid across `get_process_list` markers

- Question: "which pids were listed under slot name `:xpcshell3` and then under another name" (Android xpcshell harness).
- Command: `profiler-cli thread markers --search get_process_list --list --limit 0 --json` (54 MB), then parse each marker's Python-literal list.
- What could have shown it: nothing generic in profiler-cli; this is harness-specific and probably belongs in `fx-tests task` (see fx-tests-feedback.md).
