## `--since` rejects a date
- Command: `fx-tests test <path> --task-ids --limit 0 --since 2026-09-11`
- Expected: the failing tasks from that date on (the output prints dates everywhere, so a date is the natural input when a fix landed on a known day).
- Got: `--since expects a non-negative integer, got "2026-09-11"`.
- Workaround: count the days by hand (`--since 11`).
