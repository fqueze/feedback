## Log markers (mochitest INFO) print as "(empty)"

- Command: `profiler-cli thread markers --session <s> --category Test --list --limit 0` and `profiler-cli marker info m-67 --session <s>` on a mochitest-plain per-test profile (task RTK6ZMtuSMGjTov5Fb8cVA, profile_test_basic_autocomplete_form.html.json, content thread t-14).
- Expected: the INFO line's text, e.g. `INFO: Error: Unable to restore focus, expect failures and timeouts.`
- Got: `[(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in `marker info`. `marker info --json` shows `"value": "Error: Unable to restore focus, expect failures and timeouts."` with `"formattedValue": "(empty)"`: the value is there, the formatter drops it.
- Workaround: `marker info <handles> --json` and read `.fields[].value`. This hides exactly the diagnostic line that explains this failure.
