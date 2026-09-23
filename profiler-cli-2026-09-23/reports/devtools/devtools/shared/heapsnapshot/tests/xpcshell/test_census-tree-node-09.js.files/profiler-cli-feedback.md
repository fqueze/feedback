## Question: which xpcshell processes does each `ps` listing (mozdevice `get_process_list` DEBUG marker) contain, over a 30 s window?

- Command: `profiler-cli thread markers --session <s> --search get_process_list --list --limit 0` (after `zoom push 804,836`)
- Expected: a way to see, per marker, just the part of a long payload that matches a term (here `xpcshell`).
- Got: each row's payload is cut after ~250 characters, i.e. after `init`/`kthreadd`, so the matching entries never show; `marker info` prints the whole payload (several KB) one marker at a time.
- Workaround: `--json` and a Python regex over `flatMarkers[].fields[].value`, extracting `['<pid>', '<name matching xpcshell>', ...]`.
- What would have answered it: a `--context <term>`/match-snippet option on `thread markers --list`, printing the text around each match in the payload instead of its start.
