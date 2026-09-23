## Question: which tests fail with the "child process hang at shutdown" crash signature?

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests`
- Expected: the tests behind that message (it is the only failure message `fx-tests test` shows for test_policy_search_engine.js).
- Got: "No failure matched. Searched 4,982 tests in xpcshell-issues.json". The message only exists as a crash signature, so `failures` never sees it; `fx-tests crashes --harness xpcshell` does show it (19,349 crashes, 503 tests) but without a way to list the tests.
- Workaround: `fx-tests crashes` for the counts. Could have shown: a pointer from `failures` to `crashes` when the substring matches a crash signature, and a `--tests` option on `crashes`.
