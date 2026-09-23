## `Log` markers (mochitest `INFO` lines) print as `(empty)` although their payload has the text

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` on t-12 (content GeckoMain) of `HywJaMuTRryYs6Sbozz3eQ` `profile_test_clear_form_expiry_select_elements.html.json`; also `profiler-cli marker info m-307`.
- Expected: `INFO  add_setup | Entering` etc. -- the test's own `info()` log, which is most of what says where a mochitest-plain test was when it timed out.
- Got: `m-307  INFO  t=26.698s  instant  [(empty)] INFO: (empty)`; `marker info` shows `Level: (empty)  Message: (empty)`. `--json` has `"value": "add_setup | Entering ", "formattedValue": "(empty)"` -- the value is there, only its formatting is lost.
- Workaround: `thread markers --category Test --list --limit 0 --json | jq` over `fields[].value`, which needs a script for every read of the test log.

## `thread markers --search` does not match the text of `Log` (mochitest `INFO`) markers (review)

- Question: "when did this test log `Unable to restore focus` / `expecting a popup` / `Entering a_dummy_task`?"
- Command: `profiler-cli thread markers --search 'Unable' --list --limit 0 --session <s>` on t-15 (content GeckoMain) of `YJNkQ7j1QtWtVjB5vXCMzg`; likewise `--search 'a_dummy_task,expecting'` on t-12 of `HywJaMuTRryYs6Sbozz3eQ`.
- Expected: the `INFO` markers whose `message` field holds that text (e.g. markerIndex 11256, `Error: Unable to restore focus, expect failures and timeouts.`).
- Got: `No markers match the specified filters.` The value is in `fields[].value`, but the search seems to use the `(empty)` formatted value.
- Workaround: zoom to a narrow range, list `--category Test`, collect the `INFO` handles with awk, then `marker info <handles> --json` and a Python filter on `fields[key=message].value`. That takes three commands and a script for every log line.
