## `failures --message` misses a message that `crashes` ranks first

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the tests behind this message (or a pointer to `fx-tests crashes`).
- Got: "No failure matched. Searched 4,982 tests", while `fx-tests crashes --harness xpcshell` lists the same text as the top signature (19,349 crashes, 503 tests), and `fx-tests test` shows it as the test's first issue.
- Workaround: `fx-tests crashes --harness xpcshell`, which gives counts but not the tests.
- Question it could not answer: "which other tests fail with this crash message?"

## `task --profiles` text is silent when a failure has no per-test profile

- Command: `fx-tests task fABqTAEES1ioUmmBwJounw.0 --profiles`
- Question: "is there a per-test profile for this CRASH?"
- Expected: a line per failing test saying "no per-test profile" (CRASH entries have none) or the URL.
- Got: nothing either way in the text output; had to use `--json` to see `"testProfiles": []`.
