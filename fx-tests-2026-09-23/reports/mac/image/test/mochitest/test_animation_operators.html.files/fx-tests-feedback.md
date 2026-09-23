## Issues ranks a TEST-KNOWN-FAIL (todo) message as the failure mode

- Command: `fx-tests test image/test/mochitest/test_animation_operators.html`
- Expected: under Issues, the failing assertion: `(0) == green-background.html?clear.gif green.png` (TEST-UNEXPECTED-FAIL, 5 of 5 runs).
- Got: `5x FAIL The author of the test has indicated that flaky timeouts are expected.  Reason: untriaged` — which in the profile is a `TEST-KNOWN-FAIL` marker (SimpleTest's todo emitted for every setTimeout under requestFlakyTimeout), logged hundreds of times per run in passing runs too. The real failure only shows with `fx-tests task <id> --messages`.
- Workaround: `fx-tests task <taskId> --messages` on each task. Also `fx-tests task` prints this known-fail message as the one-line summary of the failure.

## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test image/test/mochitest/test_animation_operators.html --bugs`
- Expected: a line such as "Annotated bugs: none" after the Issues block.
- Got: the same output as without `--bugs`; only `--json` shows `"annotatedBugs": []`. "No bug" and "the lookup did not run" read the same.
- Workaround: `--bugs --json` and read `annotatedBugs`.
