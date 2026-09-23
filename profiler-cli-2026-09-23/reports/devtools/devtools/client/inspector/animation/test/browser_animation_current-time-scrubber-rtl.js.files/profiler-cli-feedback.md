## Marker times lose precision past one minute

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli thread markers --category Test --search current-time-scrubber-rtl --list --limit 0 --session browser_animation_current-time-scrubber-rtl.js-2` (and `marker info m-27 m-30 m-31`)
- Expected: times precise enough to order and space markers, e.g. `t=117.233s`.
- Got: `t=1m57s` for every marker in that second, including three that are 150 ms apart; `marker info` prints `Time: 1m57s (instant)` too.
- Workaround: `marker info ... --json` and read `start` (ms).

## Question: "was the machine saturated, or doing disk IO, while this test waited?"

- Command: `profiler-cli load .../profile_resource-usage.json --session ...-ru2`, then `profiler-cli counter list` ("No counters in this profile.") and `profiler-cli thread markers --search 'CPU Use,IO,Memory' --list` inside `zoom push 125,165`.
- Expected: something like `counter info` for CPU/IO/memory over the zoomed range: a per-bucket summary of CPU %, bytes read/written, memory used.
- Got: the resource-usage profile keeps CPU, IO and memory as ~100 ms interval markers (`CPU Use`, `IO`, `Memory`), so there are no counters, and the list shows one row per sample with no aggregate.
- Workaround: `--list --limit 0 --json`, then a Python script bucketing `data.cpuPercent`, `data.read_bytes`/`write_bytes`, `data.used` per second.
- What the output could have shown: treat these markers as counters (or give `thread markers` a numeric aggregate per field over the zoom), so "CPU avg/max, IO totals, memory over time for this range" is one command.

## Question: "which marker does this link's `marker=N` select?" (review)

- Command: `profiler-cli load '<profiler.firefox.com link with marker=6711&thread=xj>' --session ...`, then `thread markers --search 'CSS animation' --list` (13 rows at the same start time) and `marker info m-67 m-74 … --json` on all of them to find the one with `markerIndex` 6711.
- Expected: `load` of a link with `marker=N` to print the marker it selects (handle, name, label, time), or a `marker info --index N`.
- Got: `load` prints only the session status, and when a label is ambiguous, finding index N means running `marker info` on every candidate. The multi-handle `marker info --json` records also leave out their `handle`, so they can only be matched by order.
- Workaround: list the candidates, `marker info` all of them with `--json`, and match `markerIndex`.
