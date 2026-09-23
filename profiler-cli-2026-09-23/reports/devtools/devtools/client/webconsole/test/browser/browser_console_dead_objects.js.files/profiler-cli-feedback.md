## `zoom push 23m15s,23m30s` silently zooms to a 1 ns range

- Command: `profiler-cli zoom push 23m15s,23m30s --session browser_console_dead_objects.js-1` on a resource-usage profile whose marker list prints times as `t=23m21s`.
- Expected: either a 15 s zoom (the format the tool itself prints) or an error.
- Got: `Zoom depth: 1` and a view of `ts-1→ts-2 (1.000ns)` near t=0; the following `thread markers --list` showed 5 unrelated markers, which reads as "nothing happened there".
- Workaround: `zoom push 1395,1410` (seconds).
