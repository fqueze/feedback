## `fx-tests test` reports a TEST-KNOWN-FAIL (todo) as the failure mode

- Command: `fx-tests test devtools/client/inspector/test/highlighter/browser_inspector_highlighter-cssshape_06-scale.js`
- Expected: "Issues (first failure per run)" to show the first TEST-UNEXPECTED-FAIL, `#ellipse nw moved right after nw scale - Didn't expect +0, but got it`.
- Got: `23x FAIL handleEvent() was unable to perform a11y checks on hidden node: id: , tagName: button, className: inspector-shapeswatch`. That message comes from `a11yWarn()` in AccessibilityUtils.js, which is `SimpleTest.todo(false, ...)`: the profile logs it as `TEST-KNOWN-FAIL`, once per selector, including on the selectors whose checks all pass. `fx-tests task <id> --messages` also lists it among the failing messages, sorted alphabetically with the real failures, so nothing tells it apart.
- Workaround: read the test's log in the profile (`thread markers --category Test --search <test>`), where the status column says TEST-KNOWN-FAIL vs TEST-UNEXPECTED-FAIL.
- Cost: nearly sent the diagnosis toward an a11y/hidden-swatch problem that is unrelated to the failure. Expected-fail (todo) messages should be excluded from the failure modes, or at least marked as known-fail.
