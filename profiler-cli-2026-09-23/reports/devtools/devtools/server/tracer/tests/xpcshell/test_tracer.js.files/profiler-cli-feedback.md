## Question: "which slot did each test launch in, and which processes did `ps` list at each moment?"

- Command: `profiler-cli thread markers --session <s> --search get_process_list --list --limit 0 --json`, plus the same for `launch_application` and `remotexpcshelltests.py`, then a Python script to merge them into one timeline and pull `org.mozilla.geckoview.test_runner:xpcshellN` entries out of each 12-15 KB `get_process_list` label.
- Expected: some way to read a merged, filtered timeline of several marker searches, with the label trimmed to the part that matched.
- Got: `--list` prints each label cut to the terminal width, so the matching part of a 15 KB process list is never visible. The comma-OR search merges the searches, but the payload is still cut before the match.
- Workaround: `--json` and a script (`analyze.py` in this directory).
- Suggestion: a `--context <n>` / `--match-only` option on `thread markers --list` that prints the n characters around each search match in the label instead of its head.
