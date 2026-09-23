## `--since` takes a day count, not a date

- Command: `fx-tests test <path> --task-ids --limit 0 --since 2026-09-18`
- Expected: task IDs since that date (the guide says "--day and --since filter a file the command already reads", next to dates).
- Got: `--since expects a non-negative integer, got "2026-09-18"`.
- Workaround: `--since 3`. Accepting a date too, or saying "days" in the guide's WINDOW section, would avoid it.
