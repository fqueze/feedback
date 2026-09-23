## `--since` takes days, not a date, and the guide does not say so

- Command: `fx-tests test <path> --task-ids --limit 0 --since 2026-09-20`
- Expected: task IDs since that date (the guide's THE WINDOW section mentions `--day and --since` together, and `--day` takes a date).
- Got: `fx-tests: --since expects a non-negative integer, got "2026-09-20"` on stderr (exit 1, correctly), which my pipe to grep hid.
- Workaround: `--since 2`. Saying "--since N (days)" in the guide would avoid the guess.
