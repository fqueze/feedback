## `fx-tests test <path> --bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/server/tests/xpcshell/test_get_command_and_arg.js --bugs`
- Expected: a line such as `Bugs: none name this test` (and ideally the bugs sheriffs starred its failing jobs with, here 1991833).
- Got: the same output as without `--bugs`, so "no bug" and "flag ignored" look identical.
- Workaround: `fx-tests intermittent --bug 1991833` for the bug the sibling report had found.

## One test's row in one job

- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles`
- Question: this test's outcome, message and profile in a job where 1061 tests failed.
- Got: the first 20 failures, alphabetically, then `… 1041 more (--limit 0 for all)`; needed `--limit 0` and a grep over ~5,000 lines, for each of the six jobs.
- Could have shown: a `--test <path>` filter on `fx-tests task`.
