## Question: in what order did the harness log lines within one second happen?

- Command: `profiler-cli thread markers --session test_census-tree-node-01.js-1 --search census-tree-node-01 --list --limit 0` on a 23-minute resource-usage profile.
- Expected: start times precise enough to order lines that are ~100 ms apart (the failure message vs the `get_process_list` line just before it, a `launch_application` of another slot).
- Got: `t=3m24s`, `t=3m26s` — second resolution. Everything in this investigation hinged on sub-second order.
- Workaround: `--json` and `start/1000` in a script. The list could print ms (e.g. `t=206.430s`) when the zoom/profile is long, or a `--precise-times` flag.

## Question: which processes were renamed between successive `get_process_list` (ps) DEBUG lines?

- Command: `profiler-cli thread markers --search get_process_list --list --limit 0 --json`, then a Python script parsing each ~15 KB message with `ast.literal_eval` and diffing successive listings per pid.
- Expected / what output could have shown: nothing generic in the tool; this is a Treeherder-log-shaped question. A `--diff-consecutive` for markers of one name, or simply not truncating the message in `--list` when `--search` narrows to one marker kind, would have cut the scripting.
- Workaround: script (`ps.py`, `ps2.py`, `psdiff.py` in this directory).
