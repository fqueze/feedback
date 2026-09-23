## Question: is there a bug for this test's failures? (repeat of the sibling reports' entry)

- Command: `fx-tests test devtools/server/tests/xpcshell/test_setBreakpoint-on-column.js --bugs`
- Expected: a Bugs section, saying none names the test, or the bug its failing jobs were starred on (bug 1991833).
- Got: the same output as without `--bugs`; no section.
- Workaround: took bug 1991833 from the sibling reports, then `fx-tests intermittent --bug 1991833`.

## Question: this test's row in one job

- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles` (then again with `--limit 0`, grepped)
- Expected: a way to ask for one test's row (e.g. `--test <path>`), since the default list is cut at ~20 of 488 failing tests.
- Got: no filter option; `--limit 0` prints all 488 entries.
- Workaround: `--limit 0` into a file, then grep.
