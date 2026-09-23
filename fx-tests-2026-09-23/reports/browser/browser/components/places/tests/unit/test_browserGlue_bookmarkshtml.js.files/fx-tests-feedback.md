## Question: which tests are behind this crash signature?

- Command: `fx-tests crashes --harness xpcshell --signature "storage::Service::Observe" --limit 0`
- Expected: the 28 tests carrying the signature, with counts (to see whether the crash follows a code path, here HTML documents parsed in the parent, rather than one test).
- Got: one row, `327 crashes, 28 tests`, and no test names. The names were only in `--json` (`rows[].tests[]`).
- Workaround: `--json` and reading `rows[0].tests`. A `--tests` flag like `failures` has, or printing the top tests under the row, would answer it.

## `failures --message` does not match crash signatures

- Command: `fx-tests failures --harness xpcshell --message "storage::Service::Observe" --limit 0`
- Expected: the tests failing with that crash (the Issues list of `fx-tests test` shows `CRASH @ mozilla::storage::Service::Observe` as a failure message).
- Got: `No failure matched`, suggesting a typo.
- Workaround: `fx-tests crashes --signature ... --json`. Saying "this is a crash signature, see `fx-tests crashes --signature`" would have saved the detour.

## `test --bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/places/tests/unit/test_browserGlue_bookmarkshtml.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names the test.
- Got: the normal output with no bugs section at all, which reads the same as the flag being ignored.
- Workaround: searched Bugzilla directly.
