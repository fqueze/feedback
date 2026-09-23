## Which tests were running at time t, in a resource-usage profile

- Question: "how many tests were open at once, and when did that jump?" (the harness uses at most 20 threads; 1,154 `test` markers were open from t=55s to t=95s, because every launch failed instantly and its timer reported TIMEOUT 45s later).
- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json`, then a script counting open intervals per 5s bucket.
- What could have shown it: an interval-overlap / concurrency summary for one marker name (`--group-by` has none), or `zoom push <t>,<t>` + a count of intervals covering it.

## Mapping Android xpcshell tests to their process slot

- Question: "which `:xpcshellN` slot did this test get?" (the harness does not log it when the launch fails).
- Command: all markers `--json`, then a script pairing `launch_application ... XpcshellTestRunnerService$iN` markers (which carry `_TEST_NAME` in the message) with `test` marker intervals, and reasoning over which slots were occupied at the test's start.
- Not really a profiler-cli problem: the remote harness should log the slot in its "Failed to start process" line.
