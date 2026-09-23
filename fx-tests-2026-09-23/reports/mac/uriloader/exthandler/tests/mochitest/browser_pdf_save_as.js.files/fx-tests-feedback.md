## `--since` takes a day count, not a date

- Command: `fx-tests test uriloader/exthandler/tests/mochitest/browser_pdf_save_as.js --since 2026-09-05 --coverage`
- Expected: runs per config from the fix date on (the question: "has it failed on the worst configs since the fix landed?").
- Got: `fx-tests: --since expects a non-negative integer, got "2026-09-05"`.
- Workaround: counted days back from the window's end by hand (`--since 16`). Accepting a date, or `--after <date>`, would answer "since this landing" directly.
