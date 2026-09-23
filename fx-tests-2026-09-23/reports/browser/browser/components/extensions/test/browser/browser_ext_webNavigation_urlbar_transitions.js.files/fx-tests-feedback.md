## `fx-tests test` reports TEST-KNOWN-FAIL (todo) messages as the failure mode

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_webNavigation_urlbar_transitions.js`
- Expected: "Issues (first failure per run)" to show the first TEST-UNEXPECTED-* message of each failing run, here `waiting for vsync to be disabled - timed out after 50 tries.` (all 31 runs).
- Got: all 31 runs bucketed under `FAIL handleEvent() was unable to perform a11y checks on hidden node: ...` (3 issues, 22x/7x/2x). Those come from `a11yWarn()` in AccessibilityUtils.js, which is `SimpleTest.todo()`: the per-test profile shows them as `TEST-KNOWN-FAIL`. `fx-tests task --messages` also lists them among the failure messages with no status. The real failure is only visible further down in `fx-tests task <id> --messages`.
- Workaround: read `fx-tests task <id> --messages --limit 0` and the profile's Test markers.
- Cost: the headline failure mode pointed at an a11y problem that does not exist, and split one failure mode into three.
