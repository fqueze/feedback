# fx-tests feedback — test_be_conservative.js

## `--bugs` gives no sign it looked

- Command: `fx-tests test netwerk/test/unit/test_be_conservative.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names this test.
- Got: output identical to the command without `--bugs`; no bug section and no "none found" line, so "no bug" and "flag ignored" look the same.
- Workaround: Bugzilla REST search on the summary (`/rest/bug?summary=test_be_conservative`), which found only closed bugs.
