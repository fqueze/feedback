## `--since` takes a day count, not a date

- Command: `fx-tests test toolkit/crashreporter/test/unit/test_crash_terminator.js --task-ids --limit 0 --since 2026-09-16`
- Expected: failures from that date on, as `--day` accepts a date.
- Got: `fx-tests: --since expects a non-negative integer, got "2026-09-16"`.
- Workaround: `--day <date>` once per day.
