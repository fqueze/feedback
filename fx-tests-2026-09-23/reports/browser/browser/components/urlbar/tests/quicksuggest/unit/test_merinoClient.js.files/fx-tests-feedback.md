## `--bugs` prints no bug section at all
- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_merinoClient.js --bugs`
- Expected: a "Bugs" section, even if it only says "no bug names this test".
- Got: the same output as without `--bugs`; no line about bugs, so "none found" and "flag ignored" look identical.
- Workaround: `fx-tests intermittent --test <path>` / assumed none.

## `failures --message` finds nothing for a crash signature
- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests`
- Expected: the tests behind this message (it is the test's #1 issue in `fx-tests test`).
- Got: "No failure matched" — crash signatures live only in `fx-tests crashes`. The "no match" message could point at `fx-tests crashes --signature`.
- Workaround: `fx-tests crashes --harness xpcshell --signature "child process hang" --json`.

## Question: "is this crash signature ambient, and is this test over-represented in it?"
- Command: `fx-tests crashes --harness xpcshell --signature "child process hang" --json` (then reading `.rows[0].tests`)
- The text output only gives counts (19,349 crashes, 503 tests); the per-test ranking (where this test's 103 sits among 503) needed the JSON. A `--tests` flag on `crashes` like the one on `failures` would answer it.
