## `--since` takes a day count, not the date of a step change

- Command: `fx-tests test browser/extensions/formautofill/test/browser/browser_autocomplete_secondary_action.js --since 2026-09-09 --limit 0`
- Expected: the per-config rates from the step change `--history` shows (first failure 2026-09-09), since the 21-day rates are diluted by 8 days on which the failing subtest did not exist yet.
- Got: `fx-tests: --since expects a non-negative integer, got "2026-09-09"`.
- Workaround: counted the days back from the end of the window by hand (`--since 12`). Accepting a date, the form `--history` prints, would avoid the off-by-one risk.

## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test <path> --bugs`
- Expected: a line saying no bug names the test (and which fields were searched).
- Got: the same output as without `--bugs`, with no bugs section; `--json` has `annotatedBugs: []`.
- Workaround: checked `--json`, then searched Bugzilla's REST API by summary. The question was "is there a bug for this test"; an explicit "none" would have answered it.
