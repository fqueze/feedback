## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/urlbar/tests/browser-results/browser_result_noTitle.js --bugs`
- Expected: a line such as "Bugs: none name this test" so the absence is an answer.
- Got: the same output as without `--bugs`, nothing on stdout or stderr about bugs (`annotatedBugs: []` in `--json`). Could not tell "no bug" from "flag ignored" without `--json` and a Bugzilla query.
- Workaround: `--json | .annotatedBugs`, and a Bugzilla REST summary search.
