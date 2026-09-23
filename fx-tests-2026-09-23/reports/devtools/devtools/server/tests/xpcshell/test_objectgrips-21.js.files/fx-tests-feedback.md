# fx-tests feedback (test_objectgrips-21.js)

## Question: which tests fail with the message "child process hang at shutdown"?

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests --limit 0`
- Expected: the tests behind that crash signature (it is the only failure mode of dozens of devtools xpcshell tests on macOS debug).
- Got: "No failure matched." The crash signature is not recorded as a message in xpcshell-issues.json (these runs are presumably in the "(no message recorded)" row), while `fx-tests test <path>` does show it under Issues.
- Workaround: running `fx-tests test` on each sibling test one by one.
