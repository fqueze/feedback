## Log markers (mochitest-plain `INFO` lines) print as `[(empty)] INFO: (empty)`

- Command: `profiler-cli thread markers --session <s> --category Test --list --limit 0` on the content-process thread of a mochitest-plain profile (e.g. task A0wbwr01SLWU1ug1n2B6lw, `profile_test_basic_creditcard_autocomplete_form.html.json`, thread t-8)
- Expected: the INFO text, e.g. `Error: Unable to restore focus, expect failures and timeouts.`, `add_task | Entering history_only_menu_checking`, `expecting a popup`.
- Got: every `Log` marker shows `[(empty)] INFO: (empty)`. `marker info --json` shows `fields[].value` holds the text but `formattedValue` is `"(empty)"`, and the label is built from formattedValue.
- Workaround: `--json` and read `fields[].value`. This is the test's own log in mochitest-plain, so without the workaround the most useful markers are blank.
- Question it blocked: "what was the test doing when it timed out" (the last `expecting ...` line).

## `--search message:<text>` does not match Log markers

- Command: `profiler-cli thread markers --session browser-cc-basic-3 --search "message:restore focus" --list` on t-12 (content process of `profile_test_basic_creditcard_autocomplete_form-2.html.json`, task A0wbwr01SLWU1ug1n2B6lw)
- Expected: the `INFO` Log marker whose `fields[].value` is `Error: Unable to restore focus, expect failures and timeouts.` (m-46).
- Got: "No markers match the specified filters." Probably the same root cause as the `(empty)` formattedValue above: the search reads the formatted value.
- Workaround: `--category Test --list --json` and filter `fields[].value` in a script.
