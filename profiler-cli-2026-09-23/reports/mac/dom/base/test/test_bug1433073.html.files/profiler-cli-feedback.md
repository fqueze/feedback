## Log markers print "(empty)" for a message that is there

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_bug1433073.html-1` (thread t-19, content process of a mochitest-plain profile), and `profiler-cli marker info m-340`.
- Expected: the INFO rows to show their text, e.g. `Error: Unable to restore focus, expect failures and timeouts.`
- Got: every `INFO` row reads `[(empty)] INFO: (empty)`, and `marker info` shows `Level: (empty)` / `Message: (empty)`. `marker info m-340 --json` has `"value": "Error: Unable to restore focus, expect failures and timeouts."` with `"formattedValue": "(empty)"`: the formatter drops the value.
- Workaround: `--json` on each INFO marker to read its message. The test's own log (the TestRunner `INFO` lines) is unreadable in the default output, which is the first thing the brief says to read.
