## `test --bugs` is silent when no bug names the test

- Command: `fx-tests test browser/components/customizableui/test/browser_885530_showInPrivateBrowsing.js --bugs`
- Expected: a "Bugs" section, saying "none found" when there are none.
- Got: the ordinary `fx-tests test` output with no mention of bugs at all, so "no bug" and "flag ignored" look the same.
- Workaround: Bugzilla REST `short_desc` search by hand (found only RESOLVED INCOMPLETE tracking bugs).
