## A TEST-UNEXPECTED-PASS failure is reported as "profile uploaded in …"

- Command: `fx-tests test devtools/client/inspector/computed/test/browser_computed_inherited-element-backed-pseudo-elements.js` (and `fx-tests task <id> --profiles --messages --full-messages`)
- Expected: the failure message, `TEST-UNEXPECTED-PASS We expect at least one assertion to fail because this test file is marked as fail-if in the manifest.`
- Got: Issues shows `3x FAIL profile uploaded in profile_browser_computed_inherited-element-backed-pseudo-elements.js.json`, and `task --messages` shows no message at all for the test. `fx-tests failures --message "We expect at least one assertion to fail"` finds nothing tree-wide.
- Workaround: loaded the per-test profile and read the `TEST-UNEXPECTED-PASS` marker. Cost one profile load before even knowing what kind of failure it was.
