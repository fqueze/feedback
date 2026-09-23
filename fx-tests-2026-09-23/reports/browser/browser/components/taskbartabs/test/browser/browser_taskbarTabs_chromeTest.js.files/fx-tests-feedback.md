# fx-tests feedback (browser_taskbarTabs_chromeTest.js)

## `--bugs` prints nothing when there are no annotated bugs

- Command: `fx-tests test browser/components/taskbartabs/test/browser/browser_taskbarTabs_chromeTest.js --bugs`
- Expected: a "Bugs" section, or a line saying no sheriff-annotated bug names this test.
- Got: the same output as without `--bugs`, no mention of bugs at all (`--json` has `annotatedBugs: []`). It reads like the flag was ignored.
- Workaround: `--json` to see `annotatedBugs: []`, then a Bugzilla REST search on the test name and on the failure message, which found bug 1938594 (the message) and bug 2057867 (a different, Linux ccov+nova skip).
- What would have answered it: "No annotated bugs name this test", ideally with a hint to search the failure message.
