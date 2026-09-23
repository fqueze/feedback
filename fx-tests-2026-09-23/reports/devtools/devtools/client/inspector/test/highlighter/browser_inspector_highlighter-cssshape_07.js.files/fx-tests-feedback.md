## `test --bugs` prints nothing when there is no bug

- Command: `fx-tests test devtools/client/inspector/test/highlighter/browser_inspector_highlighter-cssshape_07.js --bugs`
- Expected: a "Bugs" section, saying "none found" when empty.
- Got: the same output as without `--bugs`, so I could not tell whether it searched. A Bugzilla summary search found two resolved-INCOMPLETE bugs (1718530, 1783036) naming the test.
- Workaround: query Bugzilla REST by hand.

## "Issues (first failure per run)" names a TEST-KNOWN-FAIL

- Command: `fx-tests test <path>` for this test
- Expected: the first TEST-UNEXPECTED-FAIL, `#ellipse nw moved right after w scale - Didn't expect +0, but got it`.
- Got: `handleEvent() was unable to perform a11y checks on hidden node: ... inspector-shapeswatch`. In the profile that is a `TEST-KNOWN-FAIL` logged on every run, the passing selector included.
- Workaround: read the test's markers in the profile.
