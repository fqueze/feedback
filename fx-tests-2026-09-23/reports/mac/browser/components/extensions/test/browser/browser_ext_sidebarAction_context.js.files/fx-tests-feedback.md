# fx-tests feedback (browser_ext_sidebarAction_context.js)

## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_sidebarAction_context.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names this test.
- Got: the same output as without `--bugs`; `--json` has `annotatedBugs: []`. Not being able to tell "none" from "the flag did nothing" meant querying Bugzilla by hand (12 old RESOLVED bugs name the test; none for this message).
