## Question: what was the machine's average CPU use over a zoomed time range?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` on a resource-usage profile (after `zoom push 560,920`), then a Python script over `--json` to average `data.cpuPercent`.
- Expected: `counter list` / `counter info` to give the CPU track of a resource-usage profile, or `thread markers` aggregated stats to summarize a numeric payload field (mean/max of `cpuPercent`) in view.
- Got: `counter list` says "No counters in this profile"; the default `thread markers` output only aggregates durations, so answering "was the machine idle during the 6-minute hang" needed a script over 3601 markers.
- Workaround: script over `--json`.

## `zoom push 15m22s,15m52s` silently zooms to a 1 ns range

- Command: `profiler-cli zoom push 15m22s,15m52s --session <s>`
- Expected: the same range as `zoom push 922,952` (the tool prints times as `15m22s` everywhere), or an error.
- Got: "View: ts-1→ts-2 (1.000ns)" with no error; the next marker list was nearly empty, which reads like "nothing happened there".
- Workaround: pass seconds.
