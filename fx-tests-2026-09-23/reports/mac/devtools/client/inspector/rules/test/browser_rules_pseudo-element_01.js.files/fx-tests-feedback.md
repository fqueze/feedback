## Issues ranks a TEST-KNOWN-FAIL as the failure mode

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_pseudo-element_01.js`
- Expected: "Issues (first failure per run)" to show the first *unexpected* failure, `All expected elements are displayed - Got +0, expected 9`.
- Got: `23x FAIL handleEvent() was unable to perform a11y checks on hidden node: ...`. In the profile that message is `TEST-KNOWN-FAIL` (status FAIL, expected FAIL), logged three times in every run, passing or failing. `fx-tests task <id> --messages` also lists it next to the real failures without saying it was expected.
- Workaround: read the test log in the per-test profile to find the first `TEST-UNEXPECTED-FAIL`. Cost: the failure mode headline pointed at the a11y checker, not at the rule view.

## `--bugs` prints nothing when no bug matches

- Command: `fx-tests test <path> --bugs`
- Expected: a "Bugs" section, even if it only says "none open" (or lists the closed ones).
- Got: the same output as without `--bugs`, with no bugs line, so it could not be told apart from the flag being ignored.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=<test name>`, which found only closed bugs (1894709, 1931322, ...).
