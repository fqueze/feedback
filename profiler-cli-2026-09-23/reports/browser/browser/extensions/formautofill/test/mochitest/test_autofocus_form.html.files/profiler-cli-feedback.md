## Mochitest INFO lines cannot be read or searched without --json

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` on a mochitest-plain per-test profile (RTK6ZMtuSMGjTov5Fb8cVA, profile_test_autofocus_form.html.json, content thread t-11).
- Expected: the INFO text, e.g. `expecting a popup`, `Error: Unable to restore focus, expect failures and timeouts.`
- Got: `[(empty)] INFO: (empty)` for every Log marker. `marker info` shows `Message: (empty)` too, while `--json` has the text in `fields[].value`.
- Also: `profiler-cli profile markers --search "expecting a popup"` answers "No markers match (searched 14 threads)", although that string is the `message` payload of a Log marker on the content thread. So the test's log cannot be found by its text either.
- Workaround: dump `--json` and print `data.message` with a script (`testlog.py` in this directory). The question it answers: "what did the test log, in order, with timestamps".

## The brief's `--search <test file name>` finds nothing for mochitest-plain

- Command: `profiler-cli thread markers --category Test --search test_autofocus_form --list --limit 0` on the parent main thread t-0.
- Expected: the test's log.
- Got: 0 markers. In mochitest-plain the TestStatus/Log markers are on the content process main thread, and Log markers carry no test name, so even there the search only finds the TEST-* status lines.
- Workaround: `profile markers --search TEST-UNEXPECTED-FAIL` to find the thread, then `--category Test` without `--search` on it.
