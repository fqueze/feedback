## `--since` takes a number of days, and the guide does not say so

- Command: `fx-tests test <path> --task-ids --limit 0 --issue 1 --since 2026-09-17`
- Expected: a date to work, since the guide says "--day and --since filter" next to dates.
- Got: `--since expects a non-negative integer, got "2026-09-17"`, with exit 0 (the guide says usage errors exit 1).
- Workaround: read the full task list.
