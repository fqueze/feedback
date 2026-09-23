## `test` Issues block ranks a TODO (TEST-KNOWN-FAIL) as the top failure

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_prompts.js`
- Expected: "Issues (first failure per run)" to list the first *unexpected* result of each failing run.
- Got: row 1, `645x FAIL handleEvent() was unable to perform a11y checks on hidden node: id: main-button ...`. That message is emitted by `AccessibilityUtils.js` `a11yWarn()` → `SimpleTest.todo(false, ...)`, i.e. a TEST-KNOWN-FAIL printed on every run (the per-test profile shows it as `TEST-KNOWN-FAIL`). The same applies to `changed preference: ...` (a todo when comparePrefs is off). The runs it heads really failed later, e.g. with `This test exceeded the timeout threshold` on asan (bug 2063342). So the top row, 74% of the failures, names no failure at all, and the worst config's failure mode is hidden behind it.
- Workaround: `fx-tests task <id> --messages` per task to see the real failure.
- Also: `--issue 1 --task-ids` then lists tasks under a message that did not fail them.
