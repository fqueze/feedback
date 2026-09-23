## `--since` rejects a date
- Command: `fx-tests test <path> --task-ids --limit 0 --since 2026-09-21`
- Expected: task ids since that date (the guide says "--day and --since filter a file").
- Got: `--since expects a non-negative integer, got "2026-09-21"`, exit 0 (so a pipe hid it).
- Workaround: dumped `--task-ids --limit 0` to a file and read the last date group. Exit status should be 1 on a usage error.

## `--bugs` prints nothing when no bug names the test
- Command: `fx-tests test <path> --bugs`
- Expected: a "Bugs" section, even if it says none found.
- Got: the normal report with no bug section at all, so "no bug" and "flag ignored" look the same.
- Workaround: Bugzilla quicksearch via curl.
