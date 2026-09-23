## `--since` takes a day count, not a date
- Command: `fx-tests test <path> --since 2026-09-18`
- Expected: runs since that date (to check the rate after a fix landed).
- Got: `fx-tests: --since expects a non-negative integer, got "2026-09-18"`. Workaround: `--since 3`.
- Could have: accepted a date too, since `--day` takes one.

## `--bugs` with no matching bug prints nothing about bugs
- Command: `fx-tests test <path> --bugs`
- Got: the normal summary, ending at Issues, with no "Bugs" section at all, so "no bug names this test" and "the flag was ignored" look the same.
- Could have shown: `Bugs: none name this test`.
