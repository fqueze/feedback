## Log markers (mochitest INFO) print "(empty)" for their message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_fullscreen-api.html-1` (thread t-13, content GeckoMain of profile_test_fullscreen-api.html.json, task ajIfcoMXQIK0m0cfD5JKyw), and `profiler-cli marker info m-12`.
- Expected: `INFO` rows showing the test's `info()` text, e.g. `SimpleTest START`.
- Got: every `INFO` row is `[(empty)] INFO: (empty)`; `marker info` shows `Level: (empty)`, `Message: (empty)`. `marker info --json` has `"value": "SimpleTest START", "formattedValue": "(empty)"` — the value is there, the formatting drops it. Same for the TestStatus `Message` field.
- Workaround: `thread markers --list --json` and print `fields[].value` myself.
- Cost: the test's own `info()` lines ("Run test file_fullscreen-X.html", "Window two is focused") are the timeline of which subtest was running; without them the list is unreadable for this test.

