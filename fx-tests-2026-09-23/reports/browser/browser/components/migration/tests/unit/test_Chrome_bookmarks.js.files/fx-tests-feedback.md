## Which other tests share this crash signature, and on which config?

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the tests behind the tree-wide crash signature (the signature `fx-tests test` shows under Issues).
- Got: "No failure matched" — crash signatures are not messages in `failures`; `fx-tests crashes` has the row (19,349 crashes, 503 tests) but no config breakdown, and `fx-tests task <id>` truncates at 20 rows without saying the other 120 are the same signature.
- Workaround: `fx-tests task <id> --profiles --limit 0` saved to a file and grepped.
