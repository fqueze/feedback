## Question: was the machine saturated while this test ran? (resource-usage profile)

- Command: `profiler-cli zoom push 146.5,153 --session <s>; profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- Expected: the machine's CPU use over the zoomed range, as `counter info` gives it for per-test profiles.
- Got: `counter list` says "No counters in this profile". The `CPU Use` markers list only a duration per row. The percentage is in `marker info`, one marker at a time, and there are 61 in 6 s.
- Workaround: `--json` and a Python script bucketing `data.cpuPercent`.
- What would have answered it: a `CPU Use` label with the percentage in the `--list` row, or a summary (min/median/max CPU %) over the zoom.

## `--search` splits on commas, so a formatted pid cannot be searched

- Command: `profiler-cli thread markers --session <s> --search "Child 8,224,pid8224" --list --limit 0`
- Expected: markers naming content process 8224. `DOCSHELL`/`DOMWINDOW` labels print it as `[Child 8,224: Main Thread]`.
- Got: 514 markers, mostly `CPU Use`/`Memory`/`IO`, because the comma split the term into `Child 8` OR `224` OR ...
- Workaround: `--search 8224` inside a zoom. It matches the raw pid.

## Default session directory not writable

- Command: `profiler-cli load <url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command (already in STATUS.md).
