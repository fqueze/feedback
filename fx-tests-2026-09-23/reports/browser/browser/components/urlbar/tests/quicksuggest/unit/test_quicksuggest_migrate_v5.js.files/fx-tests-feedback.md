## Question: which tests are behind one crash signature, and on which configs?

- Command: `fx-tests crashes --harness xpcshell --signature "child process hang at shutdown"` (also tried `--tests`, which `crashes` rejects as an unknown option).
- Expected: the list of tests behind the signature (503 tests, 19,349 crashes), like `fx-tests failures --tests` gives for a message; ideally with the configs they crash on.
- Got: one row with counts only. `--tests` is not accepted by `crashes`, and `failures --message "child process hang at shutdown"` matches nothing, because the signature is not a message.
- Workaround: none from the tool; used `fx-tests task <id> --profiles --limit 0` on one job to see the ~100 tests that crashed with it there.
- Could have shown: `--tests` on `crashes`, as on `failures`.

