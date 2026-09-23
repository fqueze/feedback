## `fx-tests test <path> --bugs` prints nothing about bugs when none is found

- Command: `fx-tests test netwerk/test/unit/test_ech_grease.js --bugs`
- Expected: a "Bugs" section, saying "none found" when empty.
- Got: the same output as without `--bugs`; only `--json` showed `annotatedBugs: []`, so "no bug" and "flag ignored" look the same.
- Workaround: `--json` and reading `annotatedBugs`.
