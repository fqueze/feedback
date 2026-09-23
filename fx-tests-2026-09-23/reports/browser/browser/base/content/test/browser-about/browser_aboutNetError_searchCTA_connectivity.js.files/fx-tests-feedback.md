## `--bugs` is silent when no bug names the test

- Command: `fx-tests test browser/base/content/test/about/browser_aboutNetError_searchCTA_connectivity.js --bugs`
- Expected: a line such as "Bugs: none name this test".
- Got: the same output as without `--bugs`; no bug section at all, so "no bug" and "flag ignored" look identical.
- Workaround: `--bugs --json` and read `annotatedBugs: []`.

