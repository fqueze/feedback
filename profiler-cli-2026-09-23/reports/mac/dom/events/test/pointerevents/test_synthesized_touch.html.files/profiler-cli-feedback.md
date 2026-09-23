## Log markers (mochitest `info()`) print as `(empty)`

- Command: `profiler-cli thread markers --session test_synthesized_touch.html-1 --thread t-16 --category Test --list --limit 0` and `profiler-cli marker info m-348 --session test_synthesized_touch.html-1` (profile `profile_test_synthesized_touch.html.json` of task GE0XzmcMRSSo-MB0_9L5-w).
- Expected: the `info()` text, e.g. `INFO  Tests for touchend`.
- Got: `INFO  [(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in `marker info`. The `--json` output has `"value": "Tests for touchend"` with `"formattedValue": "(empty)"`, so the data is there and only the formatting drops it.
- Workaround: `marker info <m> --json` and read `value` for each INFO marker you care about. Where the test logs its steps with `info()`, the default output loses them.
