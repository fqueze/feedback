## `fx-tests test <path> --bugs` prints nothing when no bug is annotated

- Command: `fx-tests test toolkit/components/telemetry/tests/unit/test_TelemetryEnvironment_search.js --bugs`
- Expected: a line such as "Annotated bugs: none" after the Issues block.
- Got: the same output as without `--bugs`, so "no bug" looks like "the flag was ignored" or "the lookup failed". Only `--json` showed `"annotatedBugs": []`.
- Workaround: `--json` and read `annotatedBugs`.

