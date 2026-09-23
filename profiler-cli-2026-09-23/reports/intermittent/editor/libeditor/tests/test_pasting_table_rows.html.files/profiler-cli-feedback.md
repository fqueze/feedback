## Log markers (mochitest INFO) print "(empty)" for Level and Message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_pasting_table_rows.html-1` (thread t-17, content process of a mochitest-plain profile, task U-JUkTp_T1mnnw3ckzDQCA), and `profiler-cli marker info m-25217`.
- Expected: `INFO  Initializing clipboard with "waitForClipboard-known-value-0.95..."...`
- Got: `INFO  [(empty)] INFO: (empty)`; `marker info` shows `Level: (empty)`, `Message: (empty)`.
- The value is there: `marker info --json` has `"value": "Initializing clipboard with ..."` with `"formattedValue": "(empty)"`. So the formatter drops it, for every Log-type marker.
- Workaround: `marker info --json` per marker. It makes the test's own INFO log (the part saying what the test was doing) unreadable in `--list`.
- Also: `--search <test file name>` misses these INFO markers entirely (their payload has no test name field), so the brief's "test's own log" command returns only TEST-* markers.
