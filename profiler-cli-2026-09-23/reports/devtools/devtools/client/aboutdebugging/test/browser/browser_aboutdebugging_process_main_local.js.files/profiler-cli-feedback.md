## `zoom push` silently accepts `14m19s,14m45s` as a 1 ns range at 14 s

- Command: `profiler-cli zoom push 14m19s,14m45s --session <s>` (times copied from `thread markers --list`, which prints `t=14m20s`)
- Expected: either the range 859 s–885 s, or an error saying minutes are not accepted.
- Got: `Pushed view range: ts-1 (14s) to ts-1 (14s) (duration: 0s)`, and the next `thread markers` call returned "No markers match".
- Workaround: `zoom push 859,885` in seconds.
