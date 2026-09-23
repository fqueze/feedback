## `test --bugs` prints nothing when no bug is found
- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_migrate_v1.js --bugs`
- Expected: a "Bugs" section, or a "no bug names this test" line.
- Got: the same output as without `--bugs`, on stdout and stderr, so "no bugs" looks the same as "flag ignored".
- Workaround: searched Bugzilla REST by hand.

## `failures --message` finds nothing for a crash signature
- Command: `fx-tests failures --harness xpcshell --message 'child process hang at shutdown'`
- Expected: the tests behind that message, since it is the test's top failure under Issues.
- Got: "No failure matched", although it is the #1 xpcshell crash signature (19,349 crashes in 503 tests).
- Workaround: `fx-tests crashes --harness xpcshell`. Suggestion: when a message matches a crash signature, have "No failure matched" point to `crashes --signature`.
