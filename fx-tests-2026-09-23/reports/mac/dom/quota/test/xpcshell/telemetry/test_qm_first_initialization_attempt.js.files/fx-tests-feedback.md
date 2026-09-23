## `--bugs` prints nothing when no bug is annotated

- Command: `fx-tests test dom/quota/test/xpcshell/telemetry/test_qm_first_initialization_attempt.js --bugs`
- Expected: a line such as "Annotated bugs: none" after the Issues block.
- Got: the same output as without `--bugs`, so "no bug" looks the same as "the flag was ignored" or "the query failed silently".
- Workaround: `--bugs --json` and read `annotatedBugs` (`[]`).

