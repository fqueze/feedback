## `--bugs` prints nothing when there are no annotated bugs

- Command: `fx-tests test browser/extensions/formautofill/test/browser/browser_autocomplete_secondary_action.js --bugs`
- Expected: a line such as "Annotated bugs: none naming this test".
- Got: output byte-identical to the run without `--bugs`, so I could not tell "no bugs" from "flag ignored / query failed" without `--json` (`annotatedBugs: []`).
- Workaround: `--bugs --json` and read `annotatedBugs`.

