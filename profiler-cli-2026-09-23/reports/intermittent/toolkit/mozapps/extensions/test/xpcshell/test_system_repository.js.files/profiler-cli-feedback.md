## Question: was the machine saturated while this test ran?

- Command: `profiler-cli profile info --session <s>` on a resource-usage profile (`.../Z3PNcOADQrWbSgfX4kZNdA/runs/0/artifacts/public/test_info/profile_resource-usage.json`), then `counter list` after `zoom push m-<test marker>`.
- Expected: some sign of the machine-wide CPU use that the profile records.
- Got: "CPU activity over time: No significant activity." and "No counters in this profile.", although the `CPU Use` markers over the same range read "CPU Percent: 100.0%, Idle %: 0.0%".
- Workaround: `thread markers --search "CPU Use" --list` inside the zoom, then `marker info` on each row.
- What would have answered it: `profile info` (or the zoomed view) summarizing the `CPU Use` markers when the profile has no samples or counters.

## Question: what is the stack of the promise behind an "uncaught rejection" failure? (review-test_system_repository.js)

- Command: `profiler-cli marker info m-194 --json` on the `TEST-UNEXPECTED-FAIL` "uncaught rejection: Unable to arm timer…" marker (Z3PNcOADQrWbSgfX4kZNdA, test_system_repository.js profile); `thread markers --search "arm timer"`.
- Expected: the rejection's stack, which says which code threw.
- Got: the marker's own capture stack (`assertNoUncaughtRejections ← _execute_test`); the matching CONSOLE_MESSAGE marker has no stack. The task's live_backing.log prints the rejection's async stack on the lines after TEST-FAIL.
- Workaround: download the task log and grep for the TEST-FAIL line.
- What would have answered it: the xpcshell harness putting the rejection stack in the TestStatus marker's fields (or `marker info` showing it, if it is already recorded somewhere).
