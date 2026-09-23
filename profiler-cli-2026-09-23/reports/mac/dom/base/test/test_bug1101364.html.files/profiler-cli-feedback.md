# profiler-cli feedback — test_bug1101364.html

## Log markers (Test category, mochitest `INFO` lines) print "(empty)" for Level and Message

- Command: `profiler-cli thread markers --session <s> --thread t-17 --category Test --list --limit 0`
  and `profiler-cli marker info m-115 --session <s>`
- Expected: the INFO line's text, e.g. `Error: Unable to restore focus, expect failures and timeouts.`
- Got: `[(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in marker info.
  `--json` shows `"value": "Error: Unable to restore focus, ..."` with `"formattedValue": "(empty)"`.
- Workaround: `marker info --json` per marker and read `.fields[].value`. The mochitest's own log
  (the thing the brief says to start from) is unreadable in the default output; the one line that
  explained the failure was hidden.
