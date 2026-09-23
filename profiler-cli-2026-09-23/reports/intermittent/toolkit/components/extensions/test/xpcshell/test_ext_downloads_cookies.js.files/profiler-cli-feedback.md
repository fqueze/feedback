## `thread markers --list`: one marker spans many lines when its text has newlines

- Command: `profiler-cli thread markers --list --limit 0 --search "MaybeWriteFileZoneId,downloads.removeFile,...,Could not remove" --session <id>`
- Expected: one row per marker, so the output can be scanned or piped as a timeline.
- Got: `INFO` markers carrying a CONSOLE_MESSAGE with a JS stack print each stack frame on its own line, and each log line exists twice (plain and quoted), so a 50-marker timeline became ~150 lines. Filtering it with grep needed a `grep -v` per stack-frame prefix.
- Workaround: `cut -c1-260 | grep -v "^_do_main\|^_execute\|^@\|^async\|..."`. A `--single-line` (escape or truncate newlines in the text column) would have avoided it.
