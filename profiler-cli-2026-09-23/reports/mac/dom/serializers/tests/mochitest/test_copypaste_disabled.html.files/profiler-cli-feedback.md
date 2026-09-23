## Log markers print "(empty)" for their level and message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_copypaste_disabled.html-1` (thread t-17, content main thread of `profile_test_copypaste_disabled.html.json` from task cheMJ1zySBGS1h2y6-nIoA), and `profiler-cli marker info m-517`.
- Expected: each mochitest `INFO` line, e.g. `Error: Unable to restore focus, expect failures and timeouts.` and `must wait for focus`, in the list and in `marker info`.
- Got: every `INFO` row reads `[(empty)] INFO: (empty)`, and `marker info` shows `Level: (empty)`, `Message: (empty)`. `marker info m-517 --json` has `"value": "Error: Unable to restore focus, expect failures and timeouts."` with `"formattedValue": "(empty)"` for the `message` field, and `"value": "INFO"`, `"formattedValue": "(empty)"` for `level`. So the formatter drops the value of `Log` markers' fields.
- Workaround: `--list --json` and jq over `.flatMarkers[].data.message`.
- Question the default output could not answer: "what did the test log (its INFO lines), in order, next to its TEST-PASS/FAIL lines?" That is the core of reading a mochitest's own log, and the plain `--list` output would have answered it if the `Log` fields were printed.
