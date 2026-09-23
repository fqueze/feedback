## Question: which tests are behind this crash signature, and how many crashes each?

- Command: `fx-tests crashes --harness xpcshell --signature "child process hang at shutdown" --limit 15`
- Expected: with the table down to one row, the tests behind the signature listed (as `failures --tests` does automatically at 3 rows or fewer), with per-test counts.
- Got: only the single signature row (19,591 crashes, 507 tests); no test list, and there is no `--tests` flag on `crashes`.
- Workaround: `--json`, where `rows[].tests[]` already carries `{test, count}` for every test. The default output could print the top N of that list.

## `failures --message` does not find a crash signature that `test` lists under Issues

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the message `fx-tests test <path>` prints under Issues ("CRASH child process hang at shutdown") to be findable, or a pointer to `crashes --signature`.
- Got: "No failure matched. ... Check --path, --message and --component for typos."
- Workaround: `fx-tests crashes --signature`. A hint in the no-match text ("this is a crash signature; try `crashes --signature`") would have saved a round trip.
