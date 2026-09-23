## `fx-tests test` reports a TEST-KNOWN-FAIL (todo) as the failure mode

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_pseudo-element_01.js`
- Expected: under "Issues (first failure per run)", `All expected elements are displayed - Got +0, expected 9`, the first TEST-UNEXPECTED-FAIL of all 23 failing runs.
- Got: `23x FAIL handleEvent() was unable to perform a11y checks on hidden node: ...`. In the profile this message is `TEST-KNOWN-FAIL` (a `SimpleTest.todo` from `AccessibilityUtils.handleEvent`), logged 3 times before the real failure. `fx-tests task <id> --profiles` repeats it as the headline message of the test.
- Workaround: `fx-tests task <id> --messages`, which lists the real messages next to it.

## `fx-tests test --bugs` prints nothing when it finds no open bug

- Command: `fx-tests test <path> --bugs`
- Expected: a "Bugs" section, or a line saying none was found (Bugzilla has 7 closed bugs naming this test, 1894709 among them).
- Got: the same output as without `--bugs`. You can't tell "no bug" from "flag ignored".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=<test file name>`.
