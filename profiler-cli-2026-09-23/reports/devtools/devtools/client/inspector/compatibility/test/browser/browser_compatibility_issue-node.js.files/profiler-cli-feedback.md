## Question: the exact time of each line of the test's log

- Command: `profiler-cli thread markers --session S --category Test --search browser_compatibility_issue-node.js --list --limit 0`
- Expected: marker times precise enough to order and subtract them (ms).
- Got: past one minute, times print as `t=1m28s` / `t=1m33s`, whole seconds. Every line of the test's log in the 1m26s–1m34s window reads `t=1m26s`…`t=1m28s`, so "when did the vsync wait start, and when did the Renderer's composite start relative to it" could not be read.
- Workaround: `--json` and a Python one-liner printing `start/1000` with 3 decimals. Needed for every range I then zoomed on.
- Could show: `t=88.748s` style (or `1m28.748s`) in `--list`, as `zoom push` accepts.

## Question: was the machine saturated at time T (resource-usage profile)

- Command: `profiler-cli thread samples --session RU --include-idle` and `profiler-cli counter list --session RU` after `zoom push 535,541`
- Expected: the machine's CPU use over the range, which the brief says the resource-usage profile holds.
- Got: "No samples in the current view" and "No counters in this profile". The CPU is only in `CPU Use` interval markers (one per 100 ms, `cpuPercent` field).
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a script to print `data.cpuPercent` per marker.
- Could show: `counter list`/`profile info` exposing the `CPU Use` markers as a CPU track with a sparkline, or `thread markers --search "CPU Use"` aggregating `cpuPercent` (min/avg/max) over the view.
