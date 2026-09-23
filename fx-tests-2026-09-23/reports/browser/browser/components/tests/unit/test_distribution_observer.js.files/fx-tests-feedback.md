## `--bugs` prints nothing when no bug names the test

- Command: `COLUMNS=250 fx-tests test browser/components/tests/unit/test_distribution_observer.js --bugs`
- Expected: a "Bugs" section, or an explicit "no sheriff-annotated bug names this test" line.
- Got: the same output as without `--bugs`, no bug section and no message on stdout or stderr, so I could not tell "no bug" from "the query did not run".
- Workaround: queried Bugzilla directly (`/rest/bug?summary=test_distribution_observer`), which returned no bug.
