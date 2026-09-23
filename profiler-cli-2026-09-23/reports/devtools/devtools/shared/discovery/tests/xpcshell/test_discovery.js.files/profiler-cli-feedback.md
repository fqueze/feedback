## Question: was a given device pid alive at each moment (Android resource-usage profile)?

- Command: `profiler-cli thread markers --session <s> --search get_process_list --list --limit 0`
- Expected: a way to find which `get_process_list` DEBUG markers contain `17816` / `xpcshell5`, e.g. `--search` matching inside the long payload, or a non-truncated message.
- Got: each row cut at about 250 characters, which is only the first few kernel threads of the `ps` list. `--search 17816` matched only the INFO lines that name the pid, not the process-list markers that contain it.
- Workaround: `--list --json` plus a Python regex over `flatMarkers[].data` to pull out `[pid, 'org.mozilla.geckoview.test_runner:xpcshellN']` per marker. That showed pid 17816 present at t=167.48–168.22 and gone from t=169.42.

## (review) Same question again: which xpcshellN processes are in each `get_process_list` marker of a window?

- Command: `profiler-cli thread markers --session <s> --search get_process_list --list --limit 0` in a `zoom push` window (MG8 602.5–604, RsfVf 888.4–890.5).
- Expected: a way to see which `org.mozilla.geckoview.test_runner:*` entries, or which pid, each process-list marker holds.
- Got: rows cut after the first kernel threads, the same as the author found.
- Workaround: `--json` plus a Python regex over `flatMarkers[].label`, a second time. A reviewer checking the report needs this too.
