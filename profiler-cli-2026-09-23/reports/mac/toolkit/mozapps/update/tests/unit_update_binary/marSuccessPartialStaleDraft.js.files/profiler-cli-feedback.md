## Which tests were running while this test's updater ran

- Command: `profiler-cli thread markers --search "status:PASS,status:TIMEOUT,status:FAIL" --list --limit 0 --json --session <s>` on a resource-usage profile, then a Python script (`overlap.py`) keeping the `test` markers whose [start, start+duration] intersects a window.
- Expected: a way to list the markers overlapping a range, e.g. `zoom push 928.7,932.0` then `thread markers --search name:test --list` returning every `test` marker that intersects the zoom, with start and end.
- Got: the list shows start and duration as `t=15m28s 32.576s` — minute-level start times in a 30-minute profile, so which tests overlap a 3 s window cannot be read from the text output.
- Workaround: `--json` plus a script.

## Machine CPU per second in a window

- Command: `thread markers --search "CPU Use" --list --limit 0 --json`, then a script averaging `data.cpuPercent` (a string like `"100.0%"`) per second.
- Expected: `thread markers --search "CPU Use"` in the default output to summarise the `cpuPercent` field (min / mean / max over the zoom, or per bucket), as `counter info` does for counters.
- Got: rows with no value in the text list; the percentage only in `--json`, as a formatted string.
- Workaround: the script (`cpu.py`).

## Session directory not writable in the sandbox

- Command: `profiler-cli load <url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message suggests `PROFILER_CLI_SESSION_DIR`, which worked.
- Cost: one failed load. Not a defect of the tool; the hint was good.
