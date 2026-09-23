## Log markers print "(empty)" for fields that have values

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` (thread: mochitest content process), then `profiler-cli marker info m-53`
- Expected: `INFO  add_setup | Entering`, i.e. the Log marker's message. These INFO lines are half the test's log.
- Got: `INFO  [(empty)] INFO: (empty)` in the list, and `Level: (empty)`, `Message: (empty)` in `marker info`. `--json` shows `"value": "add_setup | Entering "` with `"formattedValue": "(empty)"`, so the formatter drops the value.
- Workaround: `--json` and a small python script printing `data.message`.
