## Question: "which device processes carried name X, and when did a pid change name?" (Android xpcshell resource-usage profiles)

- Command: `profiler-cli thread markers --search get_process_list --list --limit 0 --json --session <s>`, then a Python script that parses each marker's Python-literal process list and tracks names per pid (`name-changes.py` next to this file).
- The `get_process_list: [[1, 'init', 'root'], …]` DEBUG markers hold ~330 processes each; the default output cuts them after the first ~20 kernel threads, so the rows that matter (`org.mozilla.geckoview.test_runner:*`) never show, and `--search` can only say a marker contains a string, not which entry.
- What would have answered it: none expected from a generic tool, but a way to print a marker's full field value (`marker info` also cuts it) would let `grep` do the rest without `--json`.
- Correction to the entry above: `marker info m-N` does print the full value (grepping it found the pids). Only `thread markers --list` cuts it. The per-pid tracking over the whole job still needed the script.
