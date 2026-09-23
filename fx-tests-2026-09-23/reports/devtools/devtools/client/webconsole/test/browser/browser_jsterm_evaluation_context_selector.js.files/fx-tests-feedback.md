## Question: is there a bug naming this test?

- Command: `fx-tests test devtools/client/webconsole/test/browser/browser_jsterm_evaluation_context_selector.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: the same output as without `--bugs`, with no bug section at all, so I could not tell "no bug" from "the flag did nothing".
- Workaround: took it as "none".

## `--since` takes a day count, not a date

- Command: `fx-tests test <path> --since 2026-09-12`
- Expected: restrict to runs from that date on (the question was "runs since the fix landed").
- Got: `--since expects a non-negative integer`. Workaround: counted days by hand (`--since 9`). Accepting a date too would save that step.
