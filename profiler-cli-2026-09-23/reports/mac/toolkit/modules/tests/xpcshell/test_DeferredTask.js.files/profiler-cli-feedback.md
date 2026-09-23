## When was a thread not running anything (gaps between consecutive markers)

- Question: in a window where timers fired late, was the main thread busy, idle, or descheduled? That is, the largest gaps between consecutive `Runnable` markers.
- Command: `profiler-cli thread markers --session S --search name:Runnable --list --limit 0 --json`, then a Python script sorting the markers and computing start(n+1) - end(n).
- What the output could have shown: a "gaps" or "coverage" summary for a marker search (largest gaps with their times, median gap), like the Frequency Analysis line but with the gap positions.

## Machine CPU while one test ran (resource-usage profile)

- Question: was the machine saturated during test X's run and its retry?
- Command: `zoom push <test marker range>` then `thread markers --search 'name:CPU Use' --list --limit 0 --json`, plus a script to print `cpuPercent` / `idle_pct` per marker. The `--list` text output shows only name, time and duration for `CPU Use`, not the percentage.
- What the output could have shown: the CPU Percent value in the list's description column, or a min/median/max of it in the aggregate view.

## `--group-by field:<key>` is case-sensitive and silent on a wrong key

- Command: `profiler-cli thread markers --session S --search name:Awake --group-by field:qos`
- Expected: groups per Quality of Service value.
- Got: a single `(no value): 328 markers` group. The key is `QoS`. No warning that no marker has a `qos` field.
- Workaround: `--json` and a script over `fields[].key == 'QoS'`.

## Default session directory not writable under a sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session ...`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message named the fix (`PROFILER_CLI_SESSION_DIR`), so this cost one round trip, but the brief does not mention it.
