
## Question: "which test and subtest does each of these failure markers belong to?"

- Needed to attribute 30 `already wrapped` TEST-UNEXPECTED-FAIL markers, spread over several test files, to their test file and task.
- Command: `profiler-cli thread markers --session <s> --search "already wrapped" --list --limit 0`, then `profiler-cli marker info m-1..m-30 --json` piped through a script that prints the `test` and `subtest` fields.
- Output that would have answered it: the `--list` row for TestStatus markers showing the `subtest` (and test file name), as the resource-usage profile's rows already append the test path.
