## Question: "is there a bug on file for this test?"

- Command: `fx-tests test browser/components/screenshots/tests/browser/browser_test_resize.js --bugs`
- Expected: a Bugs section listing bugs naming the test, or an explicit "no open bug names this test".
- Got: the same output as without `--bugs`, no Bugs section and no "none found" line, so an empty result is indistinguishable from the flag being ignored.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_test_resize.js` found two closed tracking bugs (1880529 FIXED, 1918966 INCOMPLETE) and no open one.
