## Question: what is the unexpected failure message of this test?

- Command: `fx-tests test browser/components/backup/tests/browser/browser_settings_enable_backup_encryption.js` (also `--json`, `.issues` and `.messages`)
- Expected: the Issues block to list the `TEST-UNEXPECTED-FAIL` message, `Node is not accessible via accessibility API: id: main-button, tagName: button, className: `.
- Got: `127x FAIL handleEvent() was unable to perform a11y checks on hidden node: id: input, ...`. In the per-test profiles that message is a `TEST-KNOWN-FAIL` (an `a11yWarn` / `SimpleTest.todo`) logged earlier in the same subtest; the real failure, 60 ms later, is not listed anywhere in `test` output, `--json` `issues` or `messages`. `fx-tests task <id> --messages` does list it, next to the todo, without saying which is which.
- Workaround: read the Test markers in the per-test profile.
- What would have answered it: skip TEST-KNOWN-FAIL/todo results when picking "first failure per run", or label each message with its status (FAIL vs KNOWN-FAIL) in `task --messages`.
