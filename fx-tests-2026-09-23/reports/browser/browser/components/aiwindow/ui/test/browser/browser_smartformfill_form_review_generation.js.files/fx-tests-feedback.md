## Question: "does any bug name this test?"

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartformfill_form_review_generation.js --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test" line.
- Got: the same output as without `--bugs`, with nothing about bugs at all, so "none found" and "flag ignored" look the same.
- Workaround: took it as "none found".

## Question: "runs and failures per config since a given date"

- Command: `fx-tests test <path> --since 2026-09-17 --coverage`
- Expected: a date to be accepted, as `--history` prints dates.
- Got: `--since expects a non-negative integer, got "2026-09-17"`.
- Workaround: `--since 5` (days), after working out the day count by hand.
