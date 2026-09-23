## `fx-tests test <path> --bugs` with no matching bug prints nothing to say so

- Command: `fx-tests test toolkit/components/resistfingerprinting/tests/browser/browser_usercharacteristics_mathml.js --bugs`
- Expected: a "Bugs" section, or a line such as "no bug names this test".
- Got: the same output as without `--bugs`; I could not tell "no bug" from "flag ignored".
- Workaround: none; assumed no bug.
