## `fx-tests test <path> --bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/protections/test/browser/browser_protections_lockwise.js --bugs`
- Expected: a line such as "Annotated bugs: none" so the answer is visible.
- Got: the same output as without `--bugs`, no bugs section at all, exit 0. Could not tell "none found" from "flag ignored / lookup failed" without `--json` (`annotatedBugs: []`).
- Workaround: `--json | jq .annotatedBugs`.
