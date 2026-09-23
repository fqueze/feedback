## Which pids carried a given process name over time (Android `get_process_list` markers)

- Question: which pids were listed under `org.mozilla.geckoview.test_runner:xpcshell7` in the harness's `ps` snapshots, and which of them were later listed under another name.
- Command: `profiler-cli thread markers --session <s> --search get_process_list --list --limit 0 --json` (50 MB of output for 942 markers), then a Python script `ast.literal_eval`-ing each marker's message.
- Expected: some way to search inside a long marker payload and show only the matching fragment (e.g. the `[pid, name, user]` entry that matched), instead of the whole 50 KB message truncated at the start.
- Got: the list view cuts each message to its first few hundred characters (`[[1, 'init', 'root'], [2, '[kthreadd]'…`), so the matching part is never visible; only `--json` plus a script answered it.
