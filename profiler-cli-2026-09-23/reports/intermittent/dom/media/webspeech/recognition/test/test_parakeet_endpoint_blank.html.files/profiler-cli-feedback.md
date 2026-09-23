## Test `INFO` Log markers print as "(empty)"

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` on a mochitest-plain per-test profile (content process GeckoMain), and `profiler-cli marker info m-146`.
- Expected: the INFO line's text, e.g. `add_setup | Entering setup` or the test's own `info("[diag +123ms] ...")` output, which is the test's log.
- Got: `INFO  [(empty)] INFO: (empty)` in the list, and `Level: (empty)  Message: (empty)` in `marker info`. The `--json` output has the text in `fields[].value` while `formattedValue` is `"(empty)"`.
- Workaround: `--json` and print `fields[].value` with jq. Question this cost: "what did the test log, in order, with timestamps" — the default list could answer it directly.
