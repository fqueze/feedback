## Issues list shows TEST-KNOWN-FAIL (todo) messages as the failure, and hides the real one

- Command: `fx-tests test browser/components/urlbar/tests/browser-search/browser_searchSuggestions.js`
- Expected: "Issues (first failure per run)" to list `Should find form history after adding it - [] deepEqual ["foofoo"]`, the only TEST-UNEXPECTED-FAIL, present in 16 of 16 failing runs.
- Got: six rows, all `handleEvent() was unable to perform a11y checks on hidden node: ...`, which are TEST-KNOWN-FAIL (status FAIL, expected FAIL, from `AccessibilityUtils.a11yWarn` via `test_todo`) and are logged by passing subtests too. The real failure appears nowhere in the Issues list; `fx-tests task <id>` headline repeats the a11y message too. Also splits one message into six issues by the per-run element id (`urlbarView-row-843-inner` vs `-845-`), which varies run to run.
- Workaround: `fx-tests task <id> --messages` for each of the 16 tasks, then the profile's TestStatus markers (Status/Expected fields) to see which one is unexpected.
