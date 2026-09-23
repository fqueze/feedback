## Question: "at what millisecond did each line of my test's log happen?"

- Command: `profiler-cli thread markers --session <s> --search test_browserGlue_distribution --list --limit 0`
- Expected: a timestamp precise enough to order the test's log lines against the `Process N may be hanging` / `attempting crash report` warnings (they are 25 ms apart in one case: `GL_VENDOR` at 75.427 s, SIGABRT at 75.452 s).
- Got: past the first minute, times print as `t=1m3s`, `t=1m15s`: whole seconds. About 40 log lines share `t=1m3s`.
- Workaround: the same command with `--json`, and a Python one-liner printing `start` for each handle.
- Could show: seconds with milliseconds (`t=75.427s`, as it already does below one minute) in `--list` mode.

## Question: "how busy was the machine during these 20 seconds?"

- Command: `profiler-cli zoom push 57.5,77` then `profiler-cli thread markers --search "name:CPU Use" --list` on a resource-usage profile.
- Expected: the CPU percentage of each `CPU Use` marker, or a summary for the range.
- Got: rows `m-154 CPU Use t=57.351s 238ms ✗` with no value; the percentage is only in `marker info`, one marker at a time. `counter list` says "No counters in this profile".
- Workaround: `--json` and averaging `data.cpuPercent` per second in Python.
- Could show: the marker's main field (`cpuPercent`) in the list row, or a min/avg/max of it in the grouped (non-`--list`) summary for the zoomed range.

## `✓ has stack trace` on resource-usage C++ warnings, but the stack is empty

- Command: `profiler-cli marker stack m-28 --session <s>` (a `C++ warning` listed with ✓ in a resource-usage profile).
- Expected: the warning's stack, or no ✓.
- Got: `[1] unknown!null`. Same for `marker info m-59`.
- Workaround: none; gave up on stacks in resource-usage profiles.
