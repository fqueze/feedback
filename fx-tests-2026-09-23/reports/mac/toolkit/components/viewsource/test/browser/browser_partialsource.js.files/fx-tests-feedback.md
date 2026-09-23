# fx-tests feedback (browser_partialsource.js)

## `--since` takes a day count, not a date

- Command: `fx-tests test toolkit/components/viewsource/test/browser/browser_partialsource.js --task-ids --limit 0 --since 2026-09-18`
- Expected: task IDs from 2026-09-18 onward. `fx-tests guide` names `--day` and `--since` together as date filters.
- Got: `fx-tests: --since expects a non-negative integer, got "2026-09-18"`
- Workaround: `--since 3`. Accepting a date as well, or naming the unit in the guide, would avoid the retry.
