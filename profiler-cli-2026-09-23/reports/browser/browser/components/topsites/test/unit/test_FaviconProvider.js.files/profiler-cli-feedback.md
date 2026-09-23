## `zoom push` with the displayed time format silently yields a 1 ns range

- Command: `profiler-cli zoom push 21m10s,21m35s --session <id>` on a 54-minute resource-usage profile, whose marker list prints times as `t=21m18s`.
- Expected: a 25 s zoom, or an error saying only seconds are accepted.
- Got: "Zoom depth: 1" and then `View: ts-1→ts-2 (1.000ns)`; the next query ran on an empty range without an error.
- Workaround: `zoom push 1270,1295`.

## Question: exact time of markers in a long profile

- Question: the order and gaps between `exiting test`, `Process 7403 may be hanging at shutdown` and `attempting crash report`, all printed as `t=21m18s`/`t=21m20s`/`t=21m30s`.
- Command: `profiler-cli thread markers --search <x> --list --limit 0 --json` piped to a script printing `start/1000` with ms.
- What could have shown it: `--list` printing ms precision (e.g. `21m18.393s`) for profiles longer than a minute.

## Question: was the machine saturated during this window?

- Question: machine CPU during the 25 s between a GPU process launch and its kill, in a resource-usage profile.
- `profiler-cli profile info` says "CPU activity over time: No significant activity" and `counter list` says "No counters", while the profile holds `CPU Use` markers at 100%.
- Command: `thread markers --search "CPU Use" --list --limit 0 --json` piped to a script averaging `data.cpuPercent` per second.
- What could have shown it: `profile info` (or a `thread cpu` summary) reading the `CPU Use` markers of resource-usage profiles as the machine's CPU track, per time bucket.

## `--search` does not match the label text the list prints

- Command: `profiler-cli thread markers --search "priority: Idle" --list --limit 0`.
- Expected: the Runnable markers whose listed label reads `... - priority: Idle (0) task: ...`.
- Got: "No markers match the specified filters."
- Workaround: `--search Idle`.
