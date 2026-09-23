## Searching the test file name does not find the test's own log lines

- Command: `profiler-cli thread markers --category Test --search test_bug560780 --list --limit 0` on the mochi.test content main thread of `profile_test_bug560780.html.json` (task RvTP9NRFTcyoxaOUB4101w).
- Expected: the test's log: its `INFO` lines (`must wait for load`, `must wait for focus`) and its TEST-PASS/FAIL lines.
- Got: only the 3 `TEST-UNEXPECTED-FAIL` markers; the `INFO` Log markers carry no test name field, and their message prints `(empty)` anyway, so a timeout in `waitForFocus` looks like a test that logged nothing.
- Workaround: `--list --limit 0 --json` for the whole Test category, then jq on `.data.message` and `.start` to find the lines in the test's time range.
- Also: in mochitest-plain the Test markers are on the content process main thread, not the parent main thread; `--category Test` on the parent only has `SpecialPowers` markers.
