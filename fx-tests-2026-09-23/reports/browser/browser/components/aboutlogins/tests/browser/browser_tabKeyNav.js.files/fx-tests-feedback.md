## `test --bugs` prints nothing when no bug is annotated

- Command: `fx-tests test browser/components/aboutlogins/tests/browser/browser_tabKeyNav.js --bugs`
- Expected: a line such as "Annotated bugs: none".
- Got: output identical to the run without `--bugs`, so "no bug" and "flag ignored / lookup failed" look the same.
- Workaround: `--bugs --json` and read `annotatedBugs` (`[]`).

