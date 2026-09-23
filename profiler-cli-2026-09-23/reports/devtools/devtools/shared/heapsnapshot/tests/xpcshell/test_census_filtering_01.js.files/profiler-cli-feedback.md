## `zoom push 4m20s,4m36s` silently zooms to a 0 s range at 4 s

- Command: `profiler-cli zoom push 4m20s,4m36s --session <id>`
- Expected: either a zoom to 260 s–276 s (the marker list prints times as `t=4m29s`, so that format is natural to paste back), or an error.
- Got: `Pushed view range: ts-1 (4s) to ts-1 (4s) (duration: 0s)`, and the next `thread markers --list` showed 10 markers from t=4 s, with no warning.
- Workaround: `zoom push 262,276` (seconds).

## Question needing a script: which process held a given name at each moment

- Question: "at each Android `get_process_list` DEBUG marker, which pid carried `…:xpcshell3`" (to see pids being renamed between snapshots).
- Command: `thread markers --search get_process_list --list --json`, then a Python regex over each marker's `data.message`.
- The default text output truncates the message at ~250 chars, so the list (hundreds of entries) was never readable without `--json`. A `marker info --full` or `--grep <text>` inside a payload would have answered it.
