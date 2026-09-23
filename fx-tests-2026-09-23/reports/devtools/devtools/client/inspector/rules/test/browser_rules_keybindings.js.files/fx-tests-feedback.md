## A TEST-KNOWN-FAIL is reported as the failure

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_keybindings.js` (also `fx-tests task RauCT43IQhaEvjEwUzR1PA --messages`)
- Expected: the Issues line ("first failure per run") gives the TEST-UNEXPECTED-FAIL, `uncaught rejection: can't access property "createElementNS", this[#document] is null`.
- Got: `FAIL handleEvent() was unable to perform a11y checks on hidden node: ... ruleview-propertyvalue theme-fg-color1`. In the profile this is a `TEST-KNOWN-FAIL` (Status FAIL, Expected FAIL, from `test_todo`), logged 5 times in the run. `task --messages` lists it as a failure message, "1x", next to the real one.
- Workaround: read the test's `Test` markers in the per-test profile to find the TEST-UNEXPECTED-FAIL. Messages with expected == status should be left out of Issues and `--messages`, or tagged as known-fail.
