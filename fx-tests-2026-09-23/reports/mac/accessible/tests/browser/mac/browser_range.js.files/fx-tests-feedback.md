## `--bugs` prints nothing when there are no annotated bugs

- Command: `fx-tests test accessible/tests/browser/mac/browser_range.js --bugs`
- Expected: a "Bugs" section, saying "none" when no sheriff-annotated bug names the test.
- Got: output identical to the run without `--bugs`, so "no bugs" cannot be told from "the flag did nothing". The answer (`"annotatedBugs": []`) was only in `--json`.
- Workaround: `--bugs --json | jq .annotatedBugs`.

