## `fx-tests test` "Issues (first failure per run)" names a TEST-KNOWN-FAIL

- Command: `fx-tests test browser/components/backup/tests/browser/browser_settings_enable_backup_encryption.js`
- Expected: issue 1 to be the first TEST-UNEXPECTED-FAIL, `Node is not accessible via accessibility API: id: main-button, tagName: button` (what sheriffs star on bug 2042220).
- Got: `127x FAIL handleEvent() was unable to perform a11y checks on hidden node: id: input, tagName: input`. In the profile that message is a `TEST-KNOWN-FAIL` (a11yWarn -> SimpleTest.todo), emitted before the real failure. `fx-tests task --messages` also lists it and `changed preference: ...` (also TEST-KNOWN-FAIL) as failure messages.
- Workaround: read the Test markers in the per-test profile to find which message is the TEST-UNEXPECTED-FAIL.
