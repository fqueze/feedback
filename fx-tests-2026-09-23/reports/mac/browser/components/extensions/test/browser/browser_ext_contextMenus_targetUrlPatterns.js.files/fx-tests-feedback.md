# fx-tests feedback

## `--bugs` prints nothing, not even "none found"

- Command: `COLUMNS=250 fx-tests test browser/components/extensions/test/browser/browser_ext_contextMenus_targetUrlPatterns.js --bugs`
- Expected: a Bugs section listing the sheriff-annotated bugs naming the test (its old single tracking bug 1775558 is RESOLVED FIXED, so possibly none in the window), or an explicit line saying none were found.
- Got: stdout identical to the same command without `--bugs` (checked with `diff`), nothing on stderr, exit 0. No way to tell "no bugs" from "the lookup did not run or failed silently".
- Workaround: Bugzilla REST directly (bug 1775558, bug 1478596 from the error message).
