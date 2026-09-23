## Which slot did each test hold, and which pids carried a slot's name (Android xpcshell)

- Question: for a `Could not kill left-over process` failure, which `:xpcshellN` slot the failing test held, and which slots were held by the tests running next to it.
- Command: `fx-tests task QI7tqM3WTWSvnUcSnUah6A --profiles`, then the resource-usage profile in profiler-cli.
- Expected: the slot per test (the harness knows it; it is in each `launch_application` marker as `XpcshellTestRunnerService$iN`), shown next to each test, including tests that never launched (whose slot has to be deduced by elimination).
- Got: nothing about slots; I had to dump `launch_application` and `test` markers with `--json` and join them by `_TEST_NAME` in a script, then deduce the failing test's slot by elimination.
