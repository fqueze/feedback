## `zoom push` silently accepts the tool's own time format and zooms to 1 ns

- Command: `profiler-cli zoom push 23m20s,23m25s --session <s>` (the times as printed by `thread markers --list`, e.g. `t=23m21s`)
- Expected: zoom to 1400 s..1405 s, or an error.
- Got: success, "View: ts-1→ts-2 (1.000ns)" near the profile start; the next `thread markers --list` showed 5 unrelated markers.
- Workaround: convert to seconds by hand, `zoom push 1395,1406`.
