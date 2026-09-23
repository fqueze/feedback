# fx-tests feedback (test_delete_last_profile.js.shutdown-hang)

## Question: which tests share this crash signature, and how many crashes each?
- Command: `fx-tests crashes --harness xpcshell --signature "hang at shutdown" --limit 0`
- Expected: the tests behind the signature (at least the top ones by count), since the row says 503 tests.
- Got: one row, `19,349 crashes, 503 tests`, and no way to list the tests (`--tests` exists on `failures` but not here).
- Workaround: ran `fx-tests test <path>` on each sibling test one by one to get their crash counts.

## `failures --message` silently excludes crashes
- Command: `fx-tests failures --harness xpcshell --message "hang at shutdown" --tests`
- Expected: the tests with `CRASH child process hang at shutdown` (it is listed under Issues by `fx-tests test`), or a note that crashes live in `crashes`.
- Got: `No failure matched ... Check --path, --message, --component for typos.` The hint points at typos, not at crash rows being out of scope.
- Workaround: `fx-tests crashes --signature`.
