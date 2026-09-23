## `marker info` prints "(empty)" for Log markers' Level and Message

- Command: `profiler-cli marker info m-6..m-10 --session b116083-1` (Log markers, category Test, on the mochi.test content thread of `profile_test_bug116083.html.json`, task cheMJ1zySBGS1h2y6-nIoA); `thread markers --category Test --list` shows them as `[(empty)] INFO: (empty)` too.
- Expected: the message, e.g. "must wait for focus", "Error: Unable to restore focus, expect failures and timeouts."
- Got: `Level: (empty)`, `Message: (empty)`. In `--json`, `fields[].value` holds the text and only `formattedValue` is "(empty)".
- Workaround: `marker info ... --json` and print `fields[].value`. This hides the test's own log, the most useful thing in a mochitest-plain profile.
