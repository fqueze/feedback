## Question: "is there a bug for this test?"

- Command: `fx-tests test browser/components/migration/tests/unit/test_360se_bookmarks.js --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test".
- Got: output identical to the run without `--bugs`, nothing on stderr. Only `--json` shows `annotatedBugs: []`.
- Workaround: `--json` and read `annotatedBugs`.
