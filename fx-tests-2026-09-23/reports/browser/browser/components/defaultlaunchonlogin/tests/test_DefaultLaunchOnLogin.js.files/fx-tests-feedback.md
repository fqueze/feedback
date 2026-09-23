## Question: "which tests fail with this crash signature, tree-wide?"

- Command: `fx-tests failures --message "child process hang at shutdown"`
- Expected: the tests behind the `child process hang at shutdown` crash signature, which `fx-tests test <path>` lists under Issues as `CRASH child process hang at shutdown`.
- Got: "No failure matched. Searched 4,982 tests in xpcshell-issues.json". Crash signatures are apparently not part of `failures`' messages, and nothing says so.
- Workaround: `fx-tests crashes` (19,349 crashes, 503 tests for this signature), with no per-test list there either.
- What would have answered it: `failures --message` also matching crash signatures, or a hint pointing to `fx-tests crashes`. Also a `--tests` option on `crashes` for one signature.
