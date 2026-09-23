## Which process carried a given name at each `ps` listing, over a whole job

- Question: in an Android xpcshell resource-usage profile, which pids carried the name `org.mozilla.geckoview.test_runner:xpcshell3` in each harness `get_process_list:` DEBUG marker, and when did a pid's name change.
- Command: `profiler-cli thread markers --session <s> --search "get_process_list" --list --limit 0` (plain text).
- Expected: some way to see the whole payload, or to search inside it and show the matching part.
- Got: every row is cut at the terminal width, so the plain-text list only shows the first ~10 entries (`init`, `kthreadd`, ...) of each process list. The test-runner processes are at the end and never show. `--json` for the whole job was 73 MB.
- Workaround: `--json`, then a Python script that `ast.literal_eval`s each message and tracks pid -> name across listings.
- What would have answered it: a way to print only the part of a long payload that matches the search term (e.g. `--context 200` around the match), or `marker info` over a range with no truncation, in text form.
