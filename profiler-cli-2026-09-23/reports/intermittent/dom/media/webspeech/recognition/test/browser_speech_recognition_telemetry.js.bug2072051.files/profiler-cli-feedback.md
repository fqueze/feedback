## Question: when did each process start and end, in seconds?

- Command: `profiler-cli profile info --all --session <s>`
- Expected: each process's start/end time in seconds, so "which utility process was alive at t=5.6s" and "did any process start after t=5.6s" read directly.
- Got: only timestamp handles (`[ts-7Y → ts-99]`, `[ts-9k → ts-a3]`), which say nothing without resolving each one.
- Workaround: `profile info --all --json` piped to a Python script printing `startTime`/`endTime`. The JSON had the answer; the default output did not. Printing the seconds next to the handle (e.g. `[5.284s ts-7Y → 5.426s ts-99]`) would answer it.

## Question: which markers of a filtered list fall after time T?

- Command: `profiler-cli thread markers --search "UtilityProcessManager,AcquireHWInference,..." --list --limit 0`
- Expected: a way to bound a `--search` list by time without losing the markers that started earlier.
- Got: `zoom push` also lists long-running IPC markers that started at t=1.2s and overlap the range, drowning a 30-row answer; I filtered by `t=` with awk instead.
- Suggestion: a `--start-after <t>` (or `--starting-in <range>`) option on `thread markers --list`.
