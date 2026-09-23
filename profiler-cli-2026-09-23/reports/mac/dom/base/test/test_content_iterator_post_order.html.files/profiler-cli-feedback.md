## Log-type INFO markers print "(empty)" for their message

- Command: `profiler-cli marker info m-564 --session tcipo-1` (also `thread markers --category Test --list`, which shows `[(empty)] INFO: (empty)`)
- Expected: the message text, e.g. `Error: Unable to restore focus, expect failures and timeouts.`
- Got: `Level: (empty)`, `Message: (empty)`; `--json` has `"value": "Error: Unable to restore focus, ..."` with `"formattedValue": "(empty)"`.
- Workaround: `marker info --json` and read `fields[].value`. The test's `info()` log is unreadable in the default output of every marker listing.

## `thread markers --search` does not match a Log marker's message

- Command: `profiler-cli thread markers --thread t-20 --search "restore focus" --list --limit 0 --session tcipo-1`
- Expected: the ~60 `INFO` Log markers whose message is `Error: Unable to restore focus, expect failures and timeouts.`
- Got: `0 markers`. The guide says a bare term matches all payload values.
- Workaround: `--category Test --list --limit 0 --json` and filter `data.message` in Python. Question: "when did the harness first lose focus in this session?"
