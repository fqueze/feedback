## Question: which process was started at t=39.1s (i.e. which process is ContentParent id=26)?
- Command: `profiler-cli profile info --all --session S`
- Expected: each process's start/end time in seconds, like the markers print them.
- Got: `[ts-81 → ts-E]` timestamp handles only, so matching a `Process start (immediate) for 26` marker at t=39.097s to a process needed `profile info --all --json` and a script over `startTime`/`endTime`.
- Could show: start/end in seconds next to (or instead of) the ts handles.

## Times past one minute lose their sub-second precision in marker lists
- Command: `profiler-cli thread markers --category Test --search browser_monitor_agent.js --list --limit 0 --session S`
- Expected: `t=61.234s` (or `1m1.234s`).
- Got: `t=1m`, `t=1m1s` for every marker after 60 s, so ordering and gaps between markers in the last part of the test log cannot be read. Same in `profile markers` output.
- Workaround: `--json` and read `start`.

## Question: what was the machine's CPU use (user/system/idle) over a time range, in the resource-usage profile?
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session S` (after `zoom push`), and `profiler-cli counter list` (says "No counters in this profile").
- Expected: a per-second (or per-bucket) summary of the `CPU Use` / `Memory` / `IO` markers' payloads over the zoomed range.
- Got: one row per 100 ms marker with no payload values in the list; needed `--json` and a script to bucket `user_pct`/`system_pct`/`idle_pct` per second.
- Could show: payload fields inline in `--list`, or a `counter`-like summary for resource-monitor markers.
